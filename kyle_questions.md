Why might you want to use `#fetch` instead of `#[]` when accessing hash keys?

- This can actually make a huge different for your own sanity! Take a look:

```ruby
a_hash = { ok: 'computer', not_ok: nil }
a_hash[:ok]
#=> {:ok=>"computer"}

a_hash[:not_ok]
#=> nil

a_hash[:parakeet]
#=> nil
```

This means that `#[]` will return `nil` if no key is found. There is no difference between the returned value when the key does not exist or if the key has the value `nil`

Now, `#fetch`:

```ruby
a_hash = { :ok => 'computer', :not_ok => nil }

a_hash.fetch(:ok)
#=> "computer"

a_hash.fetch(:not_ok)
#=> nil

a_hash.fetch(:parakeet)
# KeyError: key not found: :parakeet
```
Using `#fetch`, you get an actual error if the key is not found. *You want to use `#fetch`* because it gives you a more precise information about the value it is trying to fetch

***

I get confused when we create new hashes. Why do these two things get combined into a single hash?

```ruby
def test_default_value_is_the_same_object
  hash = Hash.new([])

  hash[:one] << "uno"
  hash[:two] << "dos"

  assert_equal ["uno", "dos"], hash[:one]
  assert_equal ["uno", "dos"], hash[:two]
  assert_equal ["uno", "dos"], hash[:three]

  assert_equal true, hash[:one].object_id == hash[:two].object_id
end
```

- The name of the method says everything: we are initializing a `Hash` with a **default value**. This means that all the keys will have the exact same object (the `[]`)

```ruby
hash = Hash.new([])

hash[:parakeet]
# []

hash[:parakeet].object_id
# 70230982290040

hash[:one] << "uno"
hash[:two] << "dos"

hash[:one]
#=> ['uno', 'dos']

hash[:one].object_id
# 70230982290040

hash[:two]
#=> ['uno', 'dos']

hash[:two].object_id
# 70230982290040

hash[:parakeet]
# => ['uno', 'dos']

hash[:parakeet].object_id
# 70230982290040

hash[:one].object_id == hash[:two].object_id && hash[:one].object_id == hash[:parakeet].object_id
# true
```

***

regular expressions don't make a ton of sense. cases in point:

```ruby
def test_character_classes_give_options_for_a_character
  animals = ["cat", "bat", "rat", "zat"]
  assert_equal ["cat", "bat", "rat"], animals.select { |a| a[/[cbr]at/] }     
  # I can reason about /[cbr]at/, but the rest of that makes no sense to me
end
```

- `a[/[cbr]at/]` will return the matching string or `nil`. And `select` *selects* every `|a|` (string) that does not return `nil`

```ruby
'bat'[/[cbr]at/]
# => 'bat'

'batman'[/[cbr]at/]
# => 'bat'

'parakeet'[/[cbr]at/]
# => nil

words = ['bat', 'batman', 'rat', 'zat', 'parakeet']
words.select { |word| word[/[cbr]at/] }
# => ["bat", "batman", "rat"]
```

```ruby
def test_slash_d_is_a_shortcut_for_a_digit_character_class
  assert_equal "42", "the number is 42"[/[0123456789]+/]
  assert_equal "42", "the number is 42"[/\d+/]
  # I *think* I know get that it's asking to pull out any digit within that string, and [/\d+/] == [/[0-9]+/] ?
end
```

- You are right :)

***

```ruby
assert_equal " = ", "variable_1 = 42"[/[^a-zA-Z0-9_]+/]
assert_equal " = ", "variable_1 = 42"[/\W+/]
# Why do these cut off before the 42?
```

- `/\W+/` or `/[^a-zA-Z0-9_]+/` will match any *non-word* character. And, by *non-word*, it means anything that is not *letter, number, underscore*

***

```ruby
def test_variables_can_also_be_used_to_access_captures
  assert_equal "Gray, James", "Name:  Gray, James"[/(\w+), (\w+)/]
  assert_equal "Gray", $1
  assert_equal "James", $2
# Why do these ignore "Name: "?
end
```

- Because of the comma and the space :)

It is anything that matches any *word* (opposite of *non-word* ^), followed by a comma and a space, followed by another *word*

***

```ruby
# These three:
def test_scan_is_like_find_all
  assert_equal ["one", "two", "three"], "one two-three".scan(/\w+/)
end

def test_sub_is_like_find_and_replace
  assert_equal "one t-three", "one two-three".sub(/(t\w*)/) { $1[0, 1] }
end

def test_gsub_is_like_find_and_replace_all
  assert_equal "one t-t", "one two-three".gsub(/(t\w*)/) { $1[0, 1] }
end
end
 # don't make a ton of sense to me. So we're finding all words that begin with t and replacing with... what?
```

- `scan` will put every match into an array, so the returned value for `"one two-three".scan(/\w+/)` is the array `["one", "two", "three"]`;

- `sub` will substitute the first match in the string; in the example, anything that matches `t` followed by zero or more *word*. `sub` can take a block, so when it finds a match, it passes that match to the block and replace the matching value for the returning value of the block.

In `"one two-three".sub(/(t\w*)/) { $1[0, 1] }`, the first match is `'two'`. Because it is capturing a group, `$1` is set to be `two`. Then, `'two'[0,1] # => 't'` just takes the chars from 0 to 1 (not inclusive).

For playing around with regex, there is [Rubular](http://rubular.com/r/jCLUwR2wjO)

***

# ------------- METHODS
# LOL this one almost ruined me:

```ruby
def test_calling_global_methods_with_wrong_number_of_arguments
  exception = assert_raise(ArgumentError) do
    my_global_method
  end
  assert_match(/["wrong number of arguments (0 for 2)"]/, exception.message)

  exception = assert_raise(ArgumentError) do
    my_global_method(1,2,3)
  end
  assert_match(/["wrong number of arguments (0 for 2)"]/, exception.message)
end
```

- These methods are just testing that if a method is called with the wrong number of arguments, it will throw an error. `my_global_method` takes two arguments and anything other than that will cause the error. However, I'm not sure how the second assertion is passing. It should be something like `'wrong number of arguments (3 for 2)'`
