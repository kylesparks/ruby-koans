Why might you want to use #fetch instead of #[] when accessing hash keys?

I get confused when we create new hashes. Why do these two things get combined into a single hash?
def test_default_value_is_the_same_object
  hash = Hash.new([])

  hash[:one] << "uno"
  hash[:two] << "dos"

  assert_equal ["uno", "dos"], hash[:one]
  assert_equal ["uno", "dos"], hash[:two]
  assert_equal ["uno", "dos"], hash[:three]

  assert_equal true, hash[:one].object_id == hash[:two].object_id
end


regular expressions don't make a ton of sense. cases in point:

def test_character_classes_give_options_for_a_character
  animals = ["cat", "bat", "rat", "zat"]
  assert_equal ["cat", "bat", "rat"], animals.select { |a| a[/[cbr]at/] }     
                                                      # I can reason about /[cbr]at/, but the rest of that makes no sense to me
end

def test_slash_d_is_a_shortcut_for_a_digit_character_class
  assert_equal "42", "the number is 42"[/[0123456789]+/]
  assert_equal "42", "the number is 42"[/\d+/]
                                                      # I *think* I know get that it's asking to pull out any digit within that string, and [/\d+/] == [/[0-9]+/] ?
end

assert_equal " = ", "variable_1 = 42"[/[^a-zA-Z0-9_]+/]
assert_equal " = ", "variable_1 = 42"[/\W+/]
                                                      # Why do these cut off before the 42?
                                                      

def test_variables_can_also_be_used_to_access_captures
  assert_equal "Gray, James", "Name:  Gray, James"[/(\w+), (\w+)/]
  assert_equal "Gray", $1
  assert_equal "James", $2
                                                      # Why do these ignore "Name: "?
end

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
                                                      
                                                      
# ------------- METHODS 
                                                      # LOL this one almost ruined me:
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


