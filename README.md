# Test Guide cho Ruby và Ruby on Rails

Những lợi ích chủ yếu của việc viết test:
  - giúp giảm thiểu bugs
  - cung cấp documentation cho code
  - hỗ trợ việc thiết kế code (với test first).

Những lợi ích trên hoàn toàn đúng, tuy nhiên mục đích chính của test là giúp giảm thiểu cost khi phát triển phần mềm. Nếu việc viết, maintain và chạy test tiêu tốn nhiều thời gian hơn việc fix bug, viết documentation và thiết kế code thì việc viết test là thừa và không một người nào làm điều này.

Những người mới làm quen với test thường cảm thấy mệt mỏi và chán nản với việc viết test là do test của họ thường cost nhiều hơn là giá trị mà test của họ mang lại. Còn những người chưa quen với việc viết test trước sẽ cảm thấy hiệu năng làm việc giảm và thế là họ cũng dần dần bỏ quên việc viết test.

Giải pháp cho những vấn đề trên không phải là ngừng viết test mà là ta phải học cách viết test tốt hơn. Để viết test tốt hơn thì ta cần phải biết được mục đích viết test và phải biết test cái gì, test khi nào và test như thế nào.

## 1. Mục đích viết test

Test có rất nhiều lợi ích. Có những lợi ích thì có thể thấy ngay và có những lợi ích chưa thể nhận ra ngay được. Việc hiểu rõ các lợi ích của việc viết test sẽ giúp các bạn có động lực để viết test tốt hơn.

#### 1.1 Tìm bugs

Việc tìm ra bugs sớm trong quá trình phát triển mang lại rất nhiều lợi ích. Việc fix bugs ở giai đoạn đầu sẽ đơn giản và dễ dàng hơn rất nhiều so với giai đoạn sau khi mà sự phụ thuộc (dependencies) trong code càng ngày càng lớn. Nếu bug được phát hiện muộn thì khi ta sửa code sẽ rất khó khăn và phức tạp, tốn nhiều công sức.

Việc fix bugs sớm sẽ giảm thiểu cost.

#### 1.2 Cung cấp documentation

Test cung cấp documentation của thiết kế một cách tin cậy hơn. Ta có thể quên dần spec theo thời gian và tài liệu thì có thể bị phân mảnh mỗi nơi không thể tổng hợp được. Tuy nhiên, nhìn vào test ta có thể suy ra được spec một cách nhanh và đơn giản nhất. Test chính là công cụ lưu trữ document tốt nhất.

#### 1.3 Hỗ trợ việc thiết kế code

Test giúp ta thiết kế code dễ dàng và tốt hơn. Khi bắt đầu code một chức năng, không phải lúc nào ta cũng có đủ các thông tin để có thể dẽ dàng thiết kế code. Sẽ có những thay đổi, những sự phụ thuộc đằng sau mà không thể lường trước được. Đôi khi ta phải viết code một cách "cứng" hơn, phục vụ chỉ một case hiện tại và chờ đợi có thêm thông tin để thiết kế tốt hơn.

Khi mà test chỉ phụ thuộc vào interfaces thì việc thay đổi code ở những tầng dưới sẽ không làm test thay đổi, nên ta có thể tự tin thay đổi thiết kế mà vẫn đảm bảo được tính năng hoạt động đúng, từ đó giảm thiểu cost.

#### 1.4 Phát hiện điểm yếu của cấu trúc code

Việc viết test cũng giúp phát hiện những điểm không tốt của cấu trúc code.

  - Nếu như việc viết test cần setup nhiều và phức tạp thì chứng tỏ là code đang thực hiện rất nhiều việc, chứa nhiều context.

  - Nếu việc viết test mà kéo theo quá nhiều object hay đoạn code khác thì chứng tỏ là code đang có nhiều sự phụ thuộc (dependencies).

  - Việc viết test khó cũng cho thấy là các object khác sẽ khó sử dụng lại code hơn

Viết code không tốt sẽ dẫn đến việc viết test khó.

Tuy nhiên không phải lúc nào việc viết test khó khăn cũng ám chỉ là thiết kế code không tốt. Hoàn toàn có thể xảy ra việc viết test không tốt cho một thiết kế cấu trúc code tốt. Do vậy, để thực sự giảm thiểu cost, cả application code và test đều cần được thiết kế một cách cẩn thận.

### Cần Test những gì

Hầu hết chúng ta thường không viết test hoặc viết quá nhiều test. Những test không cần thiết làm tăng cost của hệ thống. Ta thường có một lượng test lớn và cũ, cùng với đó là nó không bao giờ chạy.

Điều ta cần làm là viết test ít hơn và viết test đúng chỗ.

Việc DRY test sẽ giúp ta giảm cost khi mà ta phải thay đổi test theo code của app và đặt test đúng chỗ sẽ phần nào hạn chế việc đổi test do đổi code.

Một ứng dụng hướng đối tượng hoạt động theo kiểu một chuỗi các messages (method, hàm ...) được truyền đi truyền lại giữa các hộp đen là object. Do mỗi object tương ứng với một hộp đen nên ta chỉ cần quan tâm đến đầu vào (public interface) và đầu ra của nó (public interface of other objects)

Việc coi object là một hộp đen sẽ giúp ta viết test đỡ bị phụ thuộc vào application code hơn và như vậy thì khi application code bị thay đổi thì test sẽ ít có khả năng bị thay đổi hơn.

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



### Test khi nào

### Test như thế nào

## Test hàm đầu vào (incomming messages)

## Test interface (duck types)

## Test hàm private

## Test hàm đầu ra (outgoing messages)

## Test kế thừa


