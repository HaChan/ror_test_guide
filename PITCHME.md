# Test Guide cho Ruby và Ruby on Rails

Những lợi ích chủ yếu của việc viết test:
  - giúp giảm thiểu bugs
  - cung cấp documentation cho code
  - hỗ trợ việc thiết kế code (với test first).

Những lợi ích trên hoàn toàn đúng, tuy nhiên mục đích chính của test là giúp giảm thiểu cost khi phát triển phần mềm. Nếu việc viết, maintain và chạy test tiêu tốn nhiều thời gian hơn việc fix bug, viết documentation và thiết kế code thì việc viết test là thừa và không một người nào làm điều này.

Những người mới làm quen với test thường cảm thấy mệt mỏi và chán nản với việc viết test là do test của họ thường cost nhiều hơn là giá trị mà test của họ mang lại. Còn những người chưa quen với việc viết test trước sẽ cảm thấy hiệu năng làm việc giảm và thế là họ cũng dần dần bỏ quên việc viết test.

Giải pháp cho những vấn đề trên không phải là ngừng viết test mà là ta phải học cách viết test tốt hơn. Để viết test tốt hơn thì ta cần phải biết được mục đích viết test và phải biết test cái gì, test khi nào và test như thế nào.

---

## 1. Mục đích viết test

Test có rất nhiều lợi ích. Có những lợi ích thì có thể thấy ngay và có những lợi ích chưa thể nhận ra ngay được. Việc hiểu rõ các lợi ích của việc viết test sẽ giúp các bạn có động lực để viết test tốt hơn.

---

#### 1.1 Tìm bugs

Việc tìm ra bugs sớm trong quá trình phát triển mang lại rất nhiều lợi ích. Việc fix bugs ở giai đoạn đầu sẽ đơn giản và dễ dàng hơn rất nhiều so với giai đoạn sau khi mà sự phụ thuộc (dependencies) trong code càng ngày càng lớn. Nếu bug được phát hiện muộn thì khi ta sửa code sẽ rất khó khăn và phức tạp, tốn nhiều công sức.

Việc fix bugs sớm sẽ giảm thiểu cost.

---

#### 1.2 Cung cấp documentation

Test cung cấp documentation của thiết kế một cách tin cậy hơn. Ta có thể quên dần spec theo thời gian và tài liệu thì có thể bị phân mảnh mỗi nơi không thể tổng hợp được. Tuy nhiên, nhìn vào test ta có thể suy ra được spec một cách nhanh và đơn giản nhất. Test chính là công cụ lưu trữ document tốt nhất.

---

#### 1.3 Hỗ trợ việc thiết kế code

Test giúp ta thiết kế code dễ dàng và tốt hơn. Khi bắt đầu code một chức năng, không phải lúc nào ta cũng có đủ các thông tin để có thể dẽ dàng thiết kế code. Sẽ có những thay đổi, những sự phụ thuộc đằng sau mà không thể lường trước được. Đôi khi ta phải viết code một cách "cứng" hơn, phục vụ chỉ một case hiện tại và chờ đợi có thêm thông tin để thiết kế tốt hơn.

Khi mà test chỉ phụ thuộc vào interfaces thì việc thay đổi code ở những tầng dưới sẽ không làm test thay đổi, nên ta có thể tự tin thay đổi thiết kế mà vẫn đảm bảo được tính năng hoạt động đúng, từ đó giảm thiểu cost.

---

#### 1.4 Phát hiện điểm yếu của cấu trúc code

Việc viết test cũng giúp phát hiện những điểm không tốt của cấu trúc code.

  - Nếu như việc viết test cần setup nhiều và phức tạp thì chứng tỏ là code đang thực hiện rất nhiều việc, chứa nhiều context.

  - Nếu việc viết test mà kéo theo quá nhiều object hay đoạn code khác thì chứng tỏ là code đang có nhiều sự phụ thuộc (dependencies).

  - Việc viết test khó cũng cho thấy là các object khác sẽ khó sử dụng lại code hơn

Viết code không tốt sẽ dẫn đến việc viết test khó.

Tuy nhiên không phải lúc nào việc viết test khó khăn cũng ám chỉ là thiết kế code không tốt. Hoàn toàn có thể xảy ra việc viết test không tốt cho một thiết kế cấu trúc code tốt. Do vậy, để thực sự giảm thiểu cost, cả application code và test đều cần được thiết kế một cách cẩn thận.

---

### Cần Test những gì

Hầu hết chúng ta thường không viết test hoặc viết quá nhiều test. Những test không cần thiết làm tăng cost của hệ thống. Ta thường có một lượng test lớn và cũ, cùng với đó là nó không bao giờ chạy.

Điều ta cần làm là viết test ít hơn và viết test đúng chỗ.

Việc DRY test sẽ giúp ta giảm cost khi mà ta phải thay đổi test theo code của app và đặt test đúng chỗ sẽ phần nào hạn chế việc đổi test do đổi code.

Một ứng dụng hướng đối tượng hoạt động theo kiểu một chuỗi các messages (method, hàm ...) được truyền đi truyền lại giữa các hộp đen là object. Do mỗi object tương ứng với một hộp đen nên ta chỉ cần quan tâm đến đầu vào (public interface) và đầu ra của nó (public interface of other objects)

Việc coi object là một hộp đen sẽ giúp ta viết test đỡ bị phụ thuộc vào application code hơn và như vậy thì khi application code bị thay đổi thì test sẽ ít có khả năng bị thay đổi hơn.

---

Với mỗi object, ta chỉ cần quan tâm tới những messages mà nó nhận (incomming messages) và những messages mà nó phải truyền (outgoing messages):

  - Incomming messages: chính là những hàm public của một object

  - Outgoing messages: là những hàm public của những object khác mà object đang cần test gọi tới

VD:

```ruby
class Foo
  def incomming
    Bar.new.outgoing
  end
end
```

---

- Những message mà object nhận (incomming messages) đều phải được test. Ta cần test giá trị trả về của những message này. Việc test này được gọi là test state (test trạng thái). Test state thông thường chỉ cần so sánh giá trị trả về của hàm bằng với một giá trị mong muốn nào đấy.


- Những message mà object truyền đi cho các object khác (outgoing messages) thì ta không cần phải test state cho chúng vì nó đã được test state ở chính những message của mình rồi.

---

Outgoing messages gồm 2 loại:

  - Query messages: là những method không có hiệu ứng phụ (ghi file, ghi vào DB, gọi api bên ngoài ...), những method này đơn thuần chủ trả về giá trị và không thực hiện những nhiệm vụ đặc biệt. Query messages không cần phải test bởi sending object do nó đã được test tại chính các sending object rồi (public method của mỗi object đều phải được test)

  - Command messages: là những method có hiệu ứng phụ. Sending object sẽ phải chịu trách nhiệm test *việc gọi những method* này. Sending object cần chứng minh là những message này được gọi một cách chính xác. Những test như thế này được gọi là test behavior (test hành vi). Test behavior thường bao gồm việc test xem message được gọi bao nhiêu lần, với những tham số gì thì message mới được gửi đúng.

---

Vậy ta cần test những gì:

  - Incomming message: cần test giá trị (state) trả về của method

  - Outgoing message: cần test Command message để đảm bảo là nó được gửi một cách chính xác. Query message không cần phải test.

---

### Test khi nào

Nên viết test trước khi viết code, bất cứ khi nào có thể.

Việc viết test trước sẽ giúp ta hướng tới việc thiết kế tốt hơn, ít bị phụ thuộc vào nhau và có tính tái sử dụng cao hơn.

---

### Test như thế nào

Framework nên sử dụng: Rspec, Minitest

Test style: TDD và BDD.

  - Test Driven Design: test từ trong ra ngoài. Bắt đầu test từ các domain object rồi sử dụng các domain object để thiết kế dần các lớp bên ngoài.

  - Behavior Driven Design: Test từ ngoài vào trong. Test các object ở những lớp ngoài cùng (gần nhất với người dùng tương tác), sử dụng mock để thay thế các object chưa được định nghĩa.

Cả 2 style đều thực hiện việc viết code thông qua việc viết test trước.

---

Khi viết test, ta cần phân biệt các loại object trong app thành 2 loại:

  - Object đang test (Object under test)

  - Những object còn lại

Test chỉ nên quan tấm tới object under test và không cần quan tâm đến những object còn lại. Hay coi toàn bộ những object còn lại là không tồn tại khi khi viết test cho object under test.

Khi test ta cũng nên quan tâm tới quan điểm test (point-of-view). Nếu ở quá sâu bên trong object thì sẽ tăng tính phụ thuộc của test và code lên, làm tăng khả năng thay đổi test khi code bị thay đổi. Ta chỉ nên đứng ở bên ngoài, quan tâm đến các messeges truyền và nhận của object under test.

---

## Test hàm đầu vào (incomming messages)

Incomming message chính là những public method của một object. Những method này cần test bởi vì những object khác đều phụ thuộc vào chúng.

---

```ruby
class Gear
  attr_reader :chainring, :cog, :rim, :tire
  def initialize(chainring, cog, rim, tire)
    @chainring = chainring
    @cog       = cog
    @rim       = rim
    @tire      = tire
  end

  def gear_inches
    ratio * Wheel.new(rim, tire).diameter
  end

  def ratio
    chainring / cog.to_f
  end
end

class Wheel
  attr_reader :rim, :tire
  def initialize(rim, tire)
    @rim       = rim
    @tire      = tire
  end

  def diameter
    rim + (tire * 2)
  end
end

Gear.new(52, 11, 26, 1.5).gear_inches
```

---

### Xóa những method không sử dụng

Tất cả public message của object đều cần phải có phụ thuộc, tức là các object khác sẽ cần phải gọi tới những public message này. Nếu message nào mà không được sử dụng tại bất kỳ đâu trong ứng dụng thì những message này nên được loại bỏ khỏi ứng dụng.

---

### Đảm bảo public method chạy đúng

Public method (incomming messages) được test bằng cách đánh giá các giá trị trả về (hoặc state) khi hàm được gọi.

Yêu cầu: test method trả về đúng giá trị cho mọi trường hợp có khả năng xảy ra.

Dưới đây là ví dụ test method `diameter` của object `Wheel`:

```ruby
describe Wheel do
  let(:wheel) {Wheel.new 26, 1.5}
  describe "#diameter" do
    it {expect(wheel.diameter).to eq 29}
  end
end
```

Đây chỉ là một test đơn giản và động đến ít code nhất. Wheel không phụ thuộc vào các object khác nên không có object nào khác khởi tạo trong quá trình test `Wheel`. `Wheel` có thể hoàn toàn test độc lập trong hệ thống.

---

Với `Gear` thì mọi chuyện lại phức tạp hơn đôi chút. Ta có thể thấy là `Gear` phụ thuộc vào `Wheel` chứ không hoàn toàn độc lập.

```ruby
describe Gear do
  let(:gear) {Gear.new chainring: 52, cog: 11, rim: 26, tire: 1.5}
  describe "#gear_inches" do
    it {expect(gear.gear_inches).to eq 137}
  end
end
```

---

Mặc dù test `Gear` hoàn toàn giống `Wheel` tuy nhiên phần code tác động bởi test bên dưới `Gear` phức tạp hơn `Wheel`. Việc test `gear_inches` bao gồm việc sử dụng thêm một object khác đó là `Wheel`. Gear và Wheel phụ thuộc vào nhau trong code và do đó trong cả test nữa.

Việc `gear_inches` tạo và sử dụng một object khác sẽ ảnh hưởng đến thời gian chạy test và khả năng test fail do thay đổi code ở các phần khác nhau của code.

Nếu việc tạo `Wheel` instance mà tốn nhiều thời gian, test `Gear` sẽ bị ảnh hưởng theo mặc dù nó không cần quan tâm đến `Wheel`. Nếu như `Gear` không có lỗi mà `Wheel` lại xảy ra lỗi, test `Gear` sẽ bị fail và sẽ rất khó để phát hiện.

Test chạy nhanh nhất khi mà nó động đến ít code nhất. Khi một object phụ thuộc vào nhiều object khác được test, nó sẽ ảnh hưởng bởi không chỉ object đó mà còn các object mà nó phụ thuộc.

---

### Tách biệt Object cần test (Object under test)

Thay vì tạo object ngay bên trong một object khác, ta có thể "tiêm" (inject) object vào object thông qua hàm khởi tạo để tách biệt các object với nhau.

Ta có thể fix ví dụ trên như sau:

```ruby
class Gear
  attr_reader :chainring, :cog, :wheel
  def initialize(args)
    @chainring = args[:chainring]
    @cog       = args[:cog]
    @wheel     = args[:wheel]
  end

  def gear_inches
    # The object in the'wheel' variable plays the 'Diameterizable' role.
    ratio * wheel.diameter
  end

  def ratio
    chainring / cog.to_f
  end
end
```

---

Gear bây giờ chỉ cần quan tâm là object truyền vào có implement method `diameter`. `diameter` bây giờ tương ứng với một public interface của một _role_ nào đấy (Diameterizable).

Khi mà Gear đã được tách biệt khỏi `Wheel`, để khởi tạo Gear thì ta cần phải truyền vào một instance của `Diameterizable`.

Wheel là một `Diameterizable` nên ta sẽ sử dụng `Wheel` trong test:

```ruby
describe Gear do
  let(:gear) {Gear.new chainring: 52, cog: 11, Wheel.new(26, 1.5)}
  describe "#gear_inches" do
    it {expect(gear.gear_inches).to eq 137}
  end
end
```

---

Cách sử dụng một class cụ thể để tạo object truyền vào như thế này cũng có một số vấn đề. Nếu như việc khởi tạo `Wheel` tốn nhiều thời gian hay phức tạp thì test `Gear` sẽ phải chịu ảnh hưởng. Ngoài ra nếu ứng dụng cũng tồn tại nhiều object khác cũng có role là `Diameterizable` thì việc sử dụng một class cụ thể để test cũng là điều không hợp lý.

Vậy, thay vì truyền vào bằng một class cụ thể thì ta có thể truyền một object có chứa role. Các object sẽ quan hệ với nhau thông qua role:

  - Object ------- Behave like a ---------> Role

  - Role --------- injected into ---------> Object

Ở ví dụ trên, ta sẽ có:

  - Wheel ------- Behave like a ----------> Diameterizable

  - Diameterizable ------- injected into -----> Gear

Khi mà role chỉ có 1 object và việc tạo object đó không tốn nhiều thời gian hay phức tạp, ta có thể sử dụng object đó luôn trong test.

Tuy nhiên, khi mà role có nhiều object hay việc khởi tạo một object chứa role cực kỳ phức tạp và tốn thời gian hay giả sử ta chưa implement một object có chứa role nào cả (BDD); ta cần tạo một object giả để phục vụ cho test.

---

#### Tạo test double

**Test double** ám chỉ các object fake được sử dụng để thay thế các object thật của ứng dụng trong test.

Test double có thể đóng giả các role trong ứng dụng và thay thế việc sử dụng một object thật trong test.

Ta có thể thay thế `Wheel` bằng một test double trong test của `Gear` ở trên như sau:

```ruby
class DiameterDouble
  def diameter
    10
  end
end

describe Gear do
  let(:gear) {Gear.new chainring: 52, cog: 11, wheel: DiameterDouble.new}
  describe "#gear_inches" do
    it {expect(gear.gear_inches).to eq 137}
  end
end
```

---

Ở đây, `Diameterizable` là một double. Nó **stub** hàm `diameter` và trả một gia trị bất kỳ (ở đây là 10).

**stub** cung cấp một giá trị _tĩnh_ cho một lời gọi hàm được thực hiện trong test.

Rspec hỗ trợ việc tạo test doulbe thông qua hàm `double` và `instance_double`cùng với hỗ trợ stub lời gọi hàm thông qua `allow`:

```ruby
describe Gear do
  let(:wheel) {double("wheel")}
  let(:gear) {Gear.new chainring: 52, cog: 11, wheel: wheel}
  describe "#gear_inches" do
    before {allow(wheel).to receive(:diameter){10}
    it {expect(gear.gear_inches).to eq 137}
  end
end
```

---

Việc sử dụng test double giúp tách biệt Gear và Wheel trong test. Ta không cần phải bận tâm nếu Wheel chạy chậm nữa và test luôn đảm bảo chạy đúng.

Kiểu test trên vẫn còn một vấn đề đó là ta chưa test được role `Diameterizable` trong hệ thống. Giả sử nếu ta thay hàm `diameter` của `Wheel` thì test vẫn pass mà ứng dụng của ta sẽ gặp lỗi nếu như ta truyền Wheel vào Gear.

Khi ta tạo test double `DiameterDouble` thì tức là sỗ lượng object của role Diameterizable đã tăng lên chứ không chỉ có Wheel như trước nữa. Khi interface của một role bị thay đổi thì tất cả các object thuộc role đó cũng cần phải được update theo và ta cũng cần test để đảm bảo cho các role này.

---

#### Test Role

Test cần phải biết được có những role gì tồn tại trong ứng dụng và đảm bảo mỗi object thuộc role đều đảm bảo là mình implement interface của role.

Với ví dụ trên, ta cần đảm bảo `Wheel` và `DiameterDouble` đều implement interface của role `Diameterizable`.

Để kiểm tra mỗi object đều implement cùng một role nào đó, ta cần test mỗi object đều implement các method thuộc role.

Rspec cung cấp một tool giúp ta có thể chia sẻ những test giống nhau đó là `shared example`. Ta chỉ cần viết test 1 lần rồi include cho những object nào dùng chung.

---

Ví dụ:

- Sử dụng Ruby object làm test double

```ruby
class Wheel
  attr_reader :rim, :tire
  def initialize(rim, tire)
    @rim       = rim
    @tire      = tire
  end

  def diameter
    rim + (tire * 2)
  end
end

class DiameterDouble
  def diameter
    10
  end
end
```

---

rspec

```ruby
RSpec.shared_examples "a Diameterizable" do
  describe "#diameter" do
    it "should respond to diameter" do
      expect(diameterizable).to respond_to :diameter
    end
  end
end

RSpec.describe Wheel do
  let(:wheel) {Wheel.new 26, 1.5}
  it_behaves_like "a Diameterizable" do
    let(:diameterizable) {wheel}
  end
end

RSpec.describe DiameterDouble do
  it_behaves_like "diameterizable" do
    let(:diameterizable) {DiameterDouble.new}
  end
end
```

---

-Sử dụng `double` của rspec:

rspec

```ruby
RSpec.shared_examples "a Diameterizable" do |diameterizable|
  describe "#diameter" do
    it "should respond to diameter" do
      expect(diameterizable).to respond_to :diameter
    end
  end
end

RSpec.describe Wheel do
  let(:wheel) {Wheel.new 26, 1.5}
  it_behaves_like "a Diameterizable", wheel
end

describe Gear do
  let(:wheel) {double("wheel")}
  let(:gear) {Gear.new chainring: 52, cog: 11, wheel: wheel}
  #...
  it_behaves_like "a Diameterizable", wheel
end
```

---

## Test hàm private

Không cần test method private do đã được test ở các method public. Những method private nào mà không được sử dụng trong các method public khác thì nên xóa đi.

---

## Test hàm đầu ra (outgoing messages)

#### Query method

Query method là những method mà không có tác dụng phụ (side effect).

Ví dụ đơn giản nhất là trong `gear_inches` của Gear thì nó có gọi method `diameter` của object khác:

```ruby
class Gear
  #...
  def gear_inches
    ratio * wheel.diameter
  end
end
```

Việc test những query method này là không cần thiết do nó đã được test ở những object đã implement chúng.

---

#### Command method

Command method là những method không chỉ đơn thuần trả về một giá trị mà nó còn chứa các tác dụng phụ (side effect) khác. Object mà gọi những method này sẽ phụ thuộc vào những tác dụng phụ của nó (ví dụ: được save vào DB, được ghi vào file, gửi thông báo ...) Do vậy ta cần đảm bảo việc các command method này được gọi đúng thông qua test.

---

Ví dụ

```ruby
class RegisterUser
  def initialize user, mailer
    @user = user
    @mailer = mailer
  end

  attr_reader :user, :mailer

  def perform
    user.save
    mailer.deliver_later
  end
end
```
`user.save` sẽ làm nhiệm vụ lưu user vào DB còn `mailer.deliver_later` sẽ làm nhiệm vụ gửi mail. Cả 2 method đều là command method do chúng tồn tại side effect. Ở đây ta cần test instance của RegisterUser khi gọi hàm `perform` thì nó sẽ phải gọi hàm `save` của `user` object và hàm `deliver_later` của `mailer` object.

---

**Mock** được sử dụng để test hành vi (behavior). Mock định nghĩa sự mong đợi một method được gọi bởi một method khác khi nó được chạy.

Trong rspec, mock được thực hiện như sau:

```ruby
expect(object).to receive(:method_name).with(list_of_argument)
testing_object.send_method
```

Ví dụ:

```ruby
describe RegisterUser do
  let(:user) {double "user"}
  let(:mailer) {double "mailer"}
  let(:register_user) {RegisterUser.new user, mailer}

  describe "#perform" do
    it do
      expect(user).to receive :save
      expect(mailer).to receive :deliver_later
      register_user.perform
    end
  end
end
```

---


## Test kế thừa

```ruby
class Bicycle
  attr_reader :size, :chain, :tire_size

  def initialize(args={})
    @size       = args[:size]
    @chain      = args[:chain]     || default_chain
    @tire_size  = args[:tire_size] || default_tire_size
    post_initialize(args)
  end

  def spares
    {tire_size: tire_size, chain: chain}.merge(local_spares)
  end

  def default_tire_size
    raise NotImplementedError
  end

  # subclasses may override
  def post_initialize(args) nil; end

  def local_spares {}; end

  def default_chain '10-speed'; end
end
```

---

#### Đảm bảo các method được kế thừa đúng

Điều đầu tiên ta cần đảm bảo là tất cả các object trong cây kế thừa đều có đủ các method mà nó kế thừa từ lớp cha.

```ruby
RSpec.shared_examples "Bicycle interface" do |object|
  it "should respond to default_tire_size" do
    expect(object).to respond_to :default_tire_size
  end

  it "should respond to default_chain" do
    expect(object).to respond_to :default_chain
  end

  it "should respond to chain" do
    expect(object).to respond_to :chain
  end

  it "should respond to size" do
    expect(object).to respond_to :size
  end

  it "should respond to tire_size" do
    expect(object).to respond_to :tire_size
  end

  it "should respond to spares" do
    expect(object).to respond_to :spares
  end
end
```

Bất kỳ object nào mà pass được test trên đều có thể đóng vai trò như một `Bicycle`. Tất cả các object thuộc cây kế thừa của `Bicycle` đều phải pass hết test trên.

---

#### Xác nhận trách nhiệm của các lớp con

Do một số method mà subclass sẽ cần override từ superclass nên ta cần một test chung để đảm bảo là các subclass chắc chắn sẽ tồn tại các method này.

Ví dụ

```ruby
RSpec.shared_examples "Bicycle subclass interface" do |object|
  it "should respond to default_tire_size" do
    expect(object).to respond_to :default_tire_size
  end

  it "should respond to post_initialize" do
    expect(object).to respond_to :post_initialize
  end

  it "should respond to local_spares" do
    expect(object).to respond_to :spares
  end

  it "should respond to default_tire_size" do
    expect(object).to respond_to :tire_size
  end
end
```

---

Sau khi đã có một test chung thì ta có thể viết test riêng cho mỗi subclass.

Trong những method trên, duy chỉ có `default_tire_size` là bắt buộc phải override lại (raise NotImplementedError) nên ngoài việc test riêng từng subclass, ta cần đảm bảo superclass luôn raise lên error khi gọi method này.

```ruby
describe Bicycle do
  let(:bicyle) {Bicycle.new}
  describe "#default_tire_size" do
    it {expect{bicyle.default_tire_size}.to raise_error(NotImplementedError)}
  end
end
```

---


