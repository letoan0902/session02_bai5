# BÀI 5: Thiết kế Quy trình và Prompt Kiểm chứng Đầu ra

## 1. Bối cảnh và ý đồ thiết kế quy trình 2 bước

Tính năng cần xây dựng là kiểm tra tính hợp lệ của mã vé xe khách trực tuyến GreenBus. Mã vé hợp lệ phải có định dạng `BUS-XX-YYMMDD`, trong đó `XX` là mã tỉnh/thành gồm đúng 2 chữ cái in hoa, `YYMMDD` là ngày đi xe và ngày này không được nằm trong quá khứ.

Quy trình 2 bước được thiết kế để giảm rủi ro AI sinh mã nhìn có vẻ đúng nhưng vẫn sai ở các trường hợp biên. Bước 1 dùng AI để sinh mã nguồn theo yêu cầu nghiệp vụ. Bước 2 dùng một prompt kiểm chứng độc lập, yêu cầu AI đóng vai kỹ sư kiểm thử hoặc kiểm toán bảo mật để phản biện lại mã nguồn. Cách làm này giúp phát hiện các lỗi như xử lý ngày tháng không nghiêm ngặt, phụ thuộc múi giờ hệ thống, lỗi null, lỗi cắt chuỗi hoặc sai logic khi chuyển năm.

## 2. Prompt sinh mã nguồn

```text
Bạn là lập trình viên Java đang xây dựng tính năng kiểm tra mã vé cho hệ thống bán vé xe khách trực tuyến GreenBus.

Hãy viết class TicketValidator bằng Java để kiểm tra mã vé hợp lệ theo các quy tắc:
1. Mã vé bắt buộc bắt đầu bằng tiền tố "BUS-".
2. Tiếp theo là mã tỉnh/thành phố gồm đúng 2 ký tự chữ in hoa A-Z, ví dụ HN, SG, DN.
3. Tiếp theo là dấu gạch ngang "-".
4. Tiếp theo là 6 chữ số biểu diễn ngày đi xe theo định dạng YYMMDD.
5. Ngày đi xe không được là ngày trong quá khứ, phải lớn hơn hoặc bằng ngày hiện tại của hệ thống.

Yêu cầu xử lý lỗi biên:
1. Nếu mã vé null thì trả về false, không được gây NullPointerException.
2. Nếu mã vé rỗng hoặc chỉ gồm khoảng trắng thì trả về false.
3. Nếu sai định dạng thì trả về false.
4. Nếu ngày tháng không hợp lệ, ví dụ ngày 32 tháng 13 hoặc ngày 30 tháng 2, thì trả về false.
5. Nếu ngày đi xe trong quá khứ thì trả về false.
6. Chỉ dùng thư viện chuẩn Java.

Hãy trả về:
1. Mã nguồn Java hoàn chỉnh cho class TicketValidator.
2. Một hàm main nhỏ để chạy thử vài trường hợp hợp lệ và không hợp lệ.
3. Giải thích ngắn gọn cách code xử lý từng nhóm quy tắc.
```

## 3. Prompt kiểm chứng độc lập

```text
Bạn là một kỹ sư kiểm thử và kiểm toán bảo mật độc lập. Hãy review mã nguồn Java TicketValidator dưới đây như review trước khi đưa vào hệ thống GreenBus.

Mục tiêu review:
1. Tìm lỗi logic tiềm ẩn trong xử lý định dạng BUS-XX-YYMMDD.
2. Kiểm tra nguy cơ NullPointerException, StringIndexOutOfBoundsException hoặc DateTimeException.
3. Kiểm tra cách phân tích ngày tháng có chấp nhận ngày không hợp lệ hay không, ví dụ 260230 hoặc 261332.
4. Kiểm tra logic so sánh với ngày hiện tại có gặp vấn đề khi chuyển ngày, chuyển năm hoặc khi hệ thống chạy ở múi giờ khác hay không.
5. Kiểm tra mã có dễ test tự động hay không. Nếu phụ thuộc trực tiếp vào LocalDate.now() thì hãy đề xuất cách cải tiến bằng Clock.
6. Đưa ra danh sách lỗi/rủi ro theo mức độ nghiêm trọng.
7. Viết lại phiên bản TicketValidator tối ưu cuối cùng, sử dụng java.time.LocalDate hiện đại và có khả năng test ổn định.

Mã nguồn cần review:
[Dán toàn bộ mã nguồn TicketValidator sinh ra ở Bước 1 vào đây]
```

## 4. Minh chứng thực tế dạng text log

````text
Người dùng:
Bạn là lập trình viên Java đang xây dựng tính năng kiểm tra mã vé cho hệ thống bán vé xe khách trực tuyến GreenBus.

Hãy viết class TicketValidator bằng Java để kiểm tra mã vé hợp lệ theo các quy tắc:
1. Mã vé bắt buộc bắt đầu bằng tiền tố "BUS-".
2. Tiếp theo là mã tỉnh/thành phố gồm đúng 2 ký tự chữ in hoa A-Z, ví dụ HN, SG, DN.
3. Tiếp theo là dấu gạch ngang "-".
4. Tiếp theo là 6 chữ số biểu diễn ngày đi xe theo định dạng YYMMDD.
5. Ngày đi xe không được là ngày trong quá khứ, phải lớn hơn hoặc bằng ngày hiện tại của hệ thống.

Yêu cầu xử lý lỗi biên:
1. Nếu mã vé null thì trả về false, không được gây NullPointerException.
2. Nếu mã vé rỗng hoặc chỉ gồm khoảng trắng thì trả về false.
3. Nếu sai định dạng thì trả về false.
4. Nếu ngày tháng không hợp lệ, ví dụ ngày 32 tháng 13 hoặc ngày 30 tháng 2, thì trả về false.
5. Nếu ngày đi xe trong quá khứ thì trả về false.
6. Chỉ dùng thư viện chuẩn Java.

Hãy trả về:
1. Mã nguồn Java hoàn chỉnh cho class TicketValidator.
2. Một hàm main nhỏ để chạy thử vài trường hợp hợp lệ và không hợp lệ.
3. Giải thích ngắn gọn cách code xử lý từng nhóm quy tắc.

AI:
Dưới đây là phiên bản TicketValidator đầu tiên:

```java
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.util.regex.Pattern;

public class TicketValidator {
    private static final Pattern TICKET_PATTERN =
            Pattern.compile("^BUS-[A-Z]{2}-\\d{6}$");
    private static final DateTimeFormatter FORMATTER =
            DateTimeFormatter.ofPattern("yyyyMMdd");

    public boolean isValid(String ticketCode) {
        if (ticketCode == null || ticketCode.trim().isEmpty()) {
            return false;
        }

        if (!TICKET_PATTERN.matcher(ticketCode).matches()) {
            return false;
        }

        String datePart = ticketCode.substring(7);
        LocalDate travelDate = LocalDate.parse("20" + datePart, FORMATTER);
        LocalDate today = LocalDate.now();

        return !travelDate.isBefore(today);
    }

    public static void main(String[] args) {
        TicketValidator validator = new TicketValidator();

        System.out.println(validator.isValid("BUS-HN-260615"));
        System.out.println(validator.isValid("BUS-SG-991332"));
        System.out.println(validator.isValid("BUS-DN-250101"));
        System.out.println(validator.isValid(""));
        System.out.println(validator.isValid(null));
    }
}
```

Code trên dùng regex để kiểm tra định dạng cơ bản, cắt 6 chữ số ngày tháng, chuyển thành năm 20YY và so sánh với LocalDate.now().

Người dùng:
Bạn là một kỹ sư kiểm thử và kiểm toán bảo mật độc lập. Hãy review mã nguồn Java TicketValidator dưới đây như review trước khi đưa vào hệ thống GreenBus.

Mục tiêu review:
1. Tìm lỗi logic tiềm ẩn trong xử lý định dạng BUS-XX-YYMMDD.
2. Kiểm tra nguy cơ NullPointerException, StringIndexOutOfBoundsException hoặc DateTimeException.
3. Kiểm tra cách phân tích ngày tháng có chấp nhận ngày không hợp lệ hay không, ví dụ 260230 hoặc 261332.
4. Kiểm tra logic so sánh với ngày hiện tại có gặp vấn đề khi chuyển ngày, chuyển năm hoặc khi hệ thống chạy ở múi giờ khác hay không.
5. Kiểm tra mã có dễ test tự động hay không. Nếu phụ thuộc trực tiếp vào LocalDate.now() thì hãy đề xuất cách cải tiến bằng Clock.
6. Đưa ra danh sách lỗi/rủi ro theo mức độ nghiêm trọng.
7. Viết lại phiên bản TicketValidator tối ưu cuối cùng, sử dụng java.time.LocalDate hiện đại và có khả năng test ổn định.

Mã nguồn cần review:
[Mã nguồn TicketValidator phiên bản đầu tiên ở trên]

AI:
Kết quả review độc lập:

1. Mức độ cao: DateTimeFormatter.ofPattern("yyyyMMdd") mặc định có ResolverStyle SMART. Cách parse này có thể không chặn nghiêm ngặt một số ngày không hợp lệ như 20260230 theo đúng kỳ vọng nghiệp vụ. Nên dùng parse nghiêm ngặt hoặc tách ngày tháng rồi tạo LocalDate.of để DateTimeException được xử lý rõ ràng.
2. Mức độ cao: Code không bắt DateTimeException. Nếu chuỗi có định dạng số nhưng ngày tháng không hợp lệ, hàm có thể ném exception thay vì trả về false.
3. Mức độ trung bình: Code gọi trực tiếp LocalDate.now(), làm test phụ thuộc ngày thực tế của máy chạy. Nên inject Clock để có thể test các trường hợp quá khứ, hiện tại và tương lai ổn định.
4. Mức độ trung bình: LocalDate.now() dùng múi giờ mặc định của hệ thống. Nếu server chạy ở múi giờ khác, kết quả có thể khác với ngày nghiệp vụ của GreenBus. Dùng Clock giúp cấu hình múi giờ rõ ràng hơn.
5. Mức độ thấp: Việc substring(7) hiện tại an toàn vì đã regex trước, nhưng nên dùng group regex để code rõ nghiệp vụ hơn và giảm phụ thuộc vào chỉ số cắt chuỗi.

Phiên bản tối ưu nên dùng Pattern có group, Clock, LocalDate.of và bắt DateTimeException để trả về false với ngày không hợp lệ.
````

## 5. Mã nguồn Java tối ưu cuối cùng sau kiểm duyệt

```java
import java.time.Clock;
import java.time.DateTimeException;
import java.time.LocalDate;
import java.util.Objects;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public final class TicketValidator {
    private static final Pattern TICKET_PATTERN =
            Pattern.compile("^BUS-([A-Z]{2})-(\\d{6})$");

    private final Clock clock;

    public TicketValidator() {
        this(Clock.systemDefaultZone());
    }

    public TicketValidator(Clock clock) {
        this.clock = Objects.requireNonNull(clock, "clock must not be null");
    }

    public boolean isValid(String ticketCode) {
        if (ticketCode == null || ticketCode.isBlank()) {
            return false;
        }

        Matcher matcher = TICKET_PATTERN.matcher(ticketCode);
        if (!matcher.matches()) {
            return false;
        }

        String datePart = matcher.group(2);

        try {
            LocalDate travelDate = parseTravelDate(datePart);
            LocalDate today = LocalDate.now(clock);
            return !travelDate.isBefore(today);
        } catch (DateTimeException ex) {
            return false;
        }
    }

    private static LocalDate parseTravelDate(String yymmdd) {
        int year = 2000 + Integer.parseInt(yymmdd.substring(0, 2));
        int month = Integer.parseInt(yymmdd.substring(2, 4));
        int day = Integer.parseInt(yymmdd.substring(4, 6));
        return LocalDate.of(year, month, day);
    }

    public static void main(String[] args) {
        TicketValidator validator = new TicketValidator();

        System.out.println(validator.isValid("BUS-HN-260615"));
        System.out.println(validator.isValid("BUS-SG-261332"));
        System.out.println(validator.isValid("BUS-DN-260230"));
        System.out.println(validator.isValid("BUS-H1-260615"));
        System.out.println(validator.isValid("BUS-HN-990101"));
        System.out.println(validator.isValid(""));
        System.out.println(validator.isValid(null));
    }
}
```

## 6. Giải thích phiên bản cuối

Phiên bản cuối dùng regex `^BUS-([A-Z]{2})-(\\d{6})$` để tách rõ mã tỉnh và ngày đi xe. Mã tỉnh bắt buộc là 2 chữ cái in hoa A-Z. Phần ngày bắt buộc là 6 chữ số.

Hàm `parseTravelDate` tách `YY`, `MM`, `DD`, chuyển năm thành `2000 + YY`, sau đó dùng `LocalDate.of(year, month, day)`. Nếu ngày tháng không hợp lệ, Java ném `DateTimeException` và hàm `isValid` trả về `false`.

Class nhận `Clock` qua constructor để có thể test ổn định theo ngày giả lập, đồng thời vẫn có constructor mặc định dùng `Clock.systemDefaultZone()` khi chạy thực tế. Việc so sánh ngày dùng `travelDate.isBefore(today)`, nên mã vé của hôm nay và tương lai hợp lệ, còn ngày quá khứ không hợp lệ.
