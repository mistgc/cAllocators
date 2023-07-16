<div align="center">
    <h1>Arena.h</h1>
    <p>Linear Allocator</p>
</div>

## Usage

```c
#define ARENA_IMPLEMENTATION
#include "arena.h"

#include <stdio.h>

typedef struct String {
  Arena *alloctor;

  uint8_t *text;
  size_t len;
} String;

String *string_create(Arena *a, const char *str) {
  size_t str_len = strlen(str);
  String *s = arena_alloc(a, sizeof(String));
  s->text = arena_alloc(a, str_len * sizeof(uint8_t));
  s->alloctor = a;
  s->len = str_len;
  memcpy(s->text, str, str_len);

  return s;
}

String *string_catenate_with_cstr(Arena *a, const String *s, const char *str) {
  String *new_s = arena_alloc(a, sizeof(String));
  new_s->len = s->len + strlen(str);
  new_s->text = arena_alloc(a, new_s->len * sizeof(char));
  new_s->alloctor = a;
  memcpy(new_s->text, s->text, s->len);
  memcpy(new_s->text + s->len, str, strlen(str));

  return new_s;
}

String *string_catenate(Arena *a, const String *s1, const String *s2) {
  String *new_s = arena_alloc(a, sizeof(String));
  new_s->len = s1->len + s2->len;
  new_s->text = arena_alloc(a, new_s->len * sizeof(char));
  new_s->alloctor = a;
  memcpy(new_s->text, s1->text, s1->len);
  memcpy(new_s->text + s1->len, s2->text, s2->len);

  return new_s;
}

const char *string_cstr(String *s) { return (const char *)s->text; }

void string_print(String *s) { printf("%s", string_cstr(s)); }

int main(int argc, char *argv[]) {
  Arena alloctor = {0};
  uint8_t buffer[1024];
  arena_init(&alloctor, buffer, 1024);

  String *s1 = string_create(&alloctor, "Hello");
  String *s2 = string_catenate_with_cstr(&alloctor, s1, ", World.\n");
  String *s3 = string_catenate(
      &alloctor, s2, string_create(&alloctor, "Hello, Arena Allocator!!!\n"));

  string_print(s3);

  return 0;
}
```
