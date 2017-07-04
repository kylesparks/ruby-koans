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


