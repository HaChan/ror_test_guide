# Tham khảo cách sử dụng Rspec

## Cấu trúc căn bản

Rspec sử dụng `describe` để tạo một nhóm chứa các test. Trong một block của `describe` có thể tạo các nhóm nhỏ lồng bên trong thông qua keyword `describe` hoặc `context`. Để tạo một test thì Rspec sử dụng keyword `it`.

Ví dụ:

#### 1. Một nhóm và một test

```ruby
RSpec.describe "something" do
  it "does something" do
  end
end
```

#### 2. Một nhóm có chứa các nhóm nhỏ bên trong

```ruby
RSpec.describe "something" do
  context "in one context" do
    it "does one thing" do
    end
  end

  context "in another context" do
    it "does another thing" do
    end
  end
end
```

#### 3. Cấu trúc viết test thông thường

Thông thường ta sẽ bắt đầu group của một class, sau đó ta sẽ tạo group cho từng method của class đấy (cả instance và class method).

```ruby
describe ClassName do
end
```

Với class method thì ta sử dụng format sau:

```ruby
describe ".class_method" do
end
```

Với instance method:

```ruby
describe "#instance_method" do
end
```

#### 4. Sử dụng context

Khi test một method cần test tất cả các case có thể xảy ra. Mỗi một case sẽ tương đương với một `context`.

Ví dụ:

```ruby
class Attendee
  attr_accessor :first_name, :last_name, :phone_number

  def initialize input={}
    @first_name = input[:first_name]
    @last_name = input[:last_name]
    @phone_number = clean_phone_number input[:phone_number]
  end

  def clean_phone_number phone_number
    return unless phone_number
    phone_number = phone_number.scan(/[0-9]/).join
    if phone_number.length >= 11 && phone_number.start_with?("1")
      phone_number = phone_number[1..-1]
    end
    phone_number = "0000000000" if phone_number.length != 10
    phone_number
  end
end
```

Test

```ruby
describe "#clean_phone_number" do
  context "with no phone_number" do
  end

  context "when phone_number length greater or equal 11 and start with 1" do
  end

  context "when phone_number have periods or hyphen " do
  end

  context "when phone_number length is too long" do
  end

  context "when phone_number length is too short" do
  end
end
```

Nên sử dụng `with` hoặc `when` khi bắt đầu một context. Nên miêu tả context một cách ngắn gọn nhất có thể.


#### 5. Sử dụng expect

Cấu trúc cơ bản:

```ruby
expect(actual).to matcher(expected)
expect(actual).not_to matcher(expected) #to_not is also good
```

Các built-in matcher trong Rspec

##### 5.1 Matcher về so sánh

- So sánh bằng

```ruby
expect(a).to equal(b) # object identity - a and b refer to the same object, passes if a.equal?(b)
expect(a).to eql(b)   # object equivalence - a and b have the same value, passes if a.eql?(b)
expect(a).to be == b  # object equivalence - a and b have the same value with type conversions, passes if a == b
```

- So sánh khác

```ruby
expect(actual).to be >  expected
expect(actual).to be >= expected
expect(actual).to be <= expected
expect(actual).to be <  expected
expect(actual).to be_between(minimum, maximum).inclusive
expect(actual).to be_between(minimum, maximum).exclusive
expect(actual).to match(/expression/)
expect(actual).to be_within(delta).of(expected) # work with float type
expect(actual).to start_with expected
expect(actual).to end_with expected
```

`be_within` example:

```ruby
> radius = 3
  => 3
> area_of_circle = radius * radius * Math::PI
  => 28.2743338823081
> area_of_circle == 28.2743338823081
  => false

expect(area_of_circle).to be_within(0.1).of(28.3)
```

##### 5.2 Matcher về kiểu dữ liệu, class, response method

```ruby
expect(actual).to be_instance_of(expected)
expect(actual).to be_kind_of(expected)
expect(actual).to respond_to(expected)
```

##### 5.3 Matcher về true/false/nil

```ruby
expect(actual).to be_truthy    # passes if actual is truthy (not nil or false)
expect(actual).to be true      # passes if actual == true
expect(actual).to be_falsey    # passes if actual is falsy (nil or false)
expect(actual).to be false     # passes if actual == false
expect(actual).to be_nil       # passes if actual is nil
expect(actual).to exist        # passes if actual.exist? and/or actual.exists? are truthy
expect(actual).to exist(*args) # passes if actual.exist?(*args) and/or actual.exists?(*args) are truthy
```

##### 5.4 Matcher về errors

```ruby
expect { ... }.to raise_error
expect { ... }.to raise_error(ErrorClass)
expect { ... }.to raise_error("message")
expect { ... }.to raise_error(ErrorClass, "message")
```

Example:

```ruby
  expect { raise "oops" }.to raise_error
  expect { raise "oops" }.to raise_error(RuntimeError)
  expect { raise "oops" }.to raise_error("oops")
  expect { raise "oops" }.to raise_error(/op/)
  expect { raise "oops" }.to raise_error(RuntimeError, "oops")
  expect { raise "oops" }.to raise_error(RuntimeError, /op/)
```

##### 5.5 Matcher throw

```ruby
expect { ... }.to throw_symbol
expect { ... }.to throw_symbol(:symbol)
expect { ... }.to throw_symbol(:symbol, 'value')
```

Example

```ruby
RSpec.describe "throw" do
  specify { expect { throw :foo    }.to     throw_symbol }
  specify { expect { throw :bar, 7 }.to     throw_symbol }
  specify { expect { 5 + 5         }.not_to throw_symbol }

  # deliberate failures
  specify { expect { throw :foo    }.not_to throw_symbol }
  specify { expect { throw :bar, 7 }.not_to throw_symbol }
  specify { expect { 5 + 5         }.to     throw_symbol }
end
```

##### 5.6 Matcher về collection

```ruby
expect(actual).to include(expected)
expect(array).to match_array(expected_array)
# ...which is the same as:
expect(array).to contain_exactly(individual, elements)
```

##### 5.7 Matcher về sự thay đổi state của object

```ruby
expect { object.action }.to change(object, :value).from(old).to(new)
expect { object.action }.to change(object, :value).by(delta)
expect { object.action }.to change(object, :value).by_at_least(minimum_delta)
expect { object.action }.to change(object, :value).by_at_most(maximum_delta)
```

Example:

```ruby
class Counter
  class << self
    def increment
      @count ||= 0
      @count += 1
    end

    def count
      @count ||= 0
    end
  end
end

RSpec.describe Counter, "#increment" do
  it "should increment the count" do
    expect { Counter.increment }.to change { Counter.count }.from(0).to(1)
  end

  # deliberate failure
  it "should increment the count by 2" do
    expect { Counter.increment }.to change { Counter.count }.by(2)
  end
end
```

##### 5.8 Matcher về output

```ruby
expect { actual }.to output("some output").to_stdout
expect { actual }.to output("some error").to_stderr
```

Example

```ruby
RSpec.describe "output.to_stdout matcher" do
  specify { expect { print('foo') }.to output.to_stdout }
  specify { expect { print('foo') }.to output('foo').to_stdout }
  specify { expect { print('foo') }.to output(/foo/).to_stdout }
  specify { expect { }.to_not output.to_stdout }
  specify { expect { print('foo') }.to_not output('bar').to_stdout }
  specify { expect { print('foo') }.to_not output(/bar/).to_stdout }

  # deliberate failures
  specify { expect { }.to output.to_stdout }
  specify { expect { }.to output('foo').to_stdout }
  specify { expect { print('foo') }.to_not output.to_stdout }
  specify { expect { print('foo') }.to output('bar').to_stdout }
  specify { expect { print('foo') }.to output(/bar/).to_stdout }
end
```

##### 5.9 Matcher về block call

```ruby
expect { |b| object.action(&b) }.to yield_control
expect { |b| object.action(&b) }.to yield_with_no_args           # only matches no args
expect { |b| object.action(&b) }.to yield_with_args              # matches any args
expect { |b| object.action(&b) }.to yield_successive_args(*args) # matches args against multiple yields
```

- `yield_control` test method có gọi block hay không bất kể arguments

- `yield_with_args` test method có gọi block với arguments cụ thể nào đó hay không

- `yield_with_no_args` test method có gọi block và không có arguments nào.

- `yield_successive_args` test method gọi block với số lần bằng với số phần tử mà được truyền vào. Sử dụng với các method iterators

Example

```ruby
class MyClass
  def self.yield_once_with(*args)
    yield *args
  end

  def self.yield_twice_with(*args)
    2.times { yield *args }
  end

  def self.raw_yield
    yield
  end

  def self.dont_yield
  end
end

RSpec.describe "yield_control matcher" do
  specify { expect { |b| MyClass.yield_once_with(1, &b) }.to yield_control }
  specify { expect { |b| MyClass.dont_yield(&b) }.not_to yield_control }
  specify { expect { |b| MyClass.yield_twice_with(1, &b) }.to yield_control.twice }
  specify { expect { |b| MyClass.yield_twice_with(1, &b) }.to yield_control.exactly(2).times }
  specify { expect { |b| MyClass.yield_twice_with(1, &b) }.to yield_control.at_least(1) }
  specify { expect { |b| MyClass.yield_twice_with(1, &b) }.to yield_control.at_most(3).times }
end

RSpec.describe "yield_with_args matcher" do
  specify { expect { |b| MyClass.yield_once_with("foo", &b) }.to yield_with_args }
  specify { expect { |b| MyClass.yield_once_with("foo", &b) }.to yield_with_args("foo") }
  specify { expect { |b| MyClass.yield_once_with("foo", &b) }.to yield_with_args(String) }
  specify { expect { |b| MyClass.yield_once_with("foo", &b) }.to yield_with_args(/oo/) }
end

RSpec.describe "yield_with_no_args matcher" do
  specify { expect { |b| MyClass.raw_yield(&b) }.to yield_with_no_args }
  specify { expect { |b| MyClass.dont_yield(&b) }.not_to yield_with_no_args }
  specify { expect { |b| MyClass.yield_once_with("a", &b) }.not_to yield_with_no_args }
end

RSpec.describe "yield_successive_args matcher" do
  specify { expect { |b| array.each(&b) }.to yield_successive_args(1, 2, 3) }
  specify { expect { |b| array_of_tuples.each(&b) }.to yield_successive_args([:a, :b], [:c, :d]) }
  specify { expect { |b| array.each(&b) }.to yield_successive_args(Integer, Integer, Integer) }
  specify { expect { |b| array.each(&b) }.not_to yield_successive_args(1, 2) }
end
```

Luôn luôn sử dụng `expect` thay vì `should`. Config để Rspec luôn sử dụng `expect` trong project:

```ruby
# spec_helper.rb

RSpec.configure do |config|
  # ...
  config.expect_with :rspec do |c|
    c.syntax = :expect
  end
end
```

#### 6. subject, let & let!

`subject`, `let` và `let!` là những helper method mà Rspec cung cấp giúp ta DRY các object sử dụng trong test.

`subject` có 2 loại:

  - Implicitly defined object: Mỗi Rspec test đều bắt đầu với một class cụ thể nào đó. Và một instance của class sẽ được tạo và gán vào `subject` trong toàn bộ tập test ở phía sau.

Example:

```ruby
RSpec.describe Array do
  it "should be empty when first created" do
    expect(subject).to be_empty
  end
end
```

`subject` ở ví dụ trên là một instance của `Array`.

  - Explicit Subject: Với mỗi một nhóm test, ta có thể định nghĩa một `subject` cho nhóm test đó và nó có thể là một instance bất kỳ nào đấy.

Example:

```ruby
RSpec.describe Array, "with some elements" do
  subject { [1, 2, 3] }

  it "has the prescribed elements" do
    expect(subject).to eq([1, 2, 3])
  end
end
```

`let` và `let!` giúp ta định nghĩa các biến instance sử dụng trong test.

`let` sẽ được lazy evaluation. Tức là khi nào gọi đến thì giá trị trong `let` mới được thực thi.

```ruby
$count = 0
RSpec.describe "let" do
  invocation_order = []

  let(:count) do
    invocation_order << :let
    $count += 1
  end

  it "let is lazy evaluation" do
    invocation_order << :example
    expect(invocation_order).to eq([:example])
    expect(count).to eq(1)
  end
end
```

`let!` sẽ thực thi ngay khi được định nghĩa trong block test.

```ruby
$count = 0
RSpec.describe "let!" do
  invocation_order = []

  let!(:count) do
    invocation_order << :let!
    $count += 1
  end

  it "calls the helper method in a before hook" do
    invocation_order << :example
    expect(invocation_order).to eq([:let!, :example])
    expect(count).to eq(1)
  end
end
```

Nên sử dụng `subject` để đại diện cho object cần test. Sử dụng `let` và `let!` để định nghĩa các biến hỗ trợ cho test.
