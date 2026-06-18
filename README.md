# BAI 5: Thiet ke Quy trinh va Prompt Kiem chung Dau ra

## 1. Boi canh va y do thiet ke quy trinh 2 buoc

Tinh nang can xay dung la kiem tra tinh hop le cua ma ve xe khach truc tuyen GreenBus. Ma ve hop le phai co dinh dang `BUS-XX-YYMMDD`, trong do `XX` la ma tinh/thanh gom dung 2 chu cai in hoa, `YYMMDD` la ngay di xe va ngay nay khong duoc nam trong qua khu.

Quy trinh 2 buoc duoc thiet ke de giam rui ro AI sinh ma nhin co ve dung nhung van sai o cac truong hop bien. Buoc 1 dung AI de sinh ma nguon theo yeu cau nghiep vu. Buoc 2 dung mot prompt kiem chung doc lap, yeu cau AI dong vai ky su kiem thu hoac kiem toan bao mat de phan bien lai ma nguon. Cach lam nay giup phat hien cac loi nhu xu ly ngay thang khong nghiem ngat, phu thuoc mui gio he thong, loi null, loi cat chuoi, hoac sai logic khi chuyen nam.

## 2. Prompt sinh ma nguon

```text
Ban la lap trinh vien Java dang xay dung tinh nang kiem tra ma ve cho he thong ban ve xe khach truc tuyen GreenBus.

Hay viet class TicketValidator bang Java de kiem tra ma ve hop le theo cac quy tac:
1. Ma ve bat buoc bat dau bang tien to "BUS-".
2. Tiep theo la ma tinh/thanh pho gom dung 2 ky tu chu in hoa A-Z, vi du HN, SG, DN.
3. Tiep theo la dau gach ngang "-".
4. Tiep theo la 6 chu so bieu dien ngay di xe theo dinh dang YYMMDD.
5. Ngay di xe khong duoc la ngay trong qua khu, phai lon hon hoac bang ngay hien tai cua he thong.

Yeu cau xu ly loi bien:
1. Neu ma ve null thi tra ve false, khong duoc gay NullPointerException.
2. Neu ma ve rong hoac chi gom khoang trang thi tra ve false.
3. Neu sai dinh dang thi tra ve false.
4. Neu ngay thang khong hop le, vi du ngay 32 thang 13 hoac ngay 30 thang 2, thi tra ve false.
5. Neu ngay di xe trong qua khu thi tra ve false.
6. Chi dung thu vien chuan Java.

Hay tra ve:
1. Ma nguon Java hoan chinh cho class TicketValidator.
2. Mot ham main nho de chay thu vai truong hop hop le va khong hop le.
3. Giai thich ngan gon cach code xu ly tung nhom quy tac.
```

## 3. Prompt kiem chung doc lap

```text
Ban la mot ky su kiem thu va kiem toan bao mat doc lap. Hay review ma nguon Java TicketValidator duoi day nhu review truoc khi dua vao he thong GreenBus.

Muc tieu review:
1. Tim loi logic tiem an trong xu ly dinh dang BUS-XX-YYMMDD.
2. Kiem tra nguy co NullPointerException, StringIndexOutOfBoundsException hoac DateTimeException.
3. Kiem tra cach phan tich ngay thang co chap nhan ngay khong hop le hay khong, vi du 260230 hoac 261332.
4. Kiem tra logic so sanh voi ngay hien tai co gap van de khi chuyen ngay, chuyen nam hoac khi he thong chay o mui gio khac hay khong.
5. Kiem tra ma co de test tu dong hay khong. Neu phu thuoc truc tiep vao LocalDate.now() thi hay de xuat cach cai tien bang Clock.
6. Dua ra danh sach loi/rui ro theo muc do nghiem trong.
7. Viet lai phien ban TicketValidator toi uu cuoi cung, su dung java.time.LocalDate hien dai va co kha nang test on dinh.

Ma nguon can review:
[Dan toan bo ma nguon TicketValidator sinh ra o Buoc 1 vao day]
```

## 4. Minh chung thuc te dang text log

````text
Nguoi dung:
Ban la lap trinh vien Java dang xay dung tinh nang kiem tra ma ve cho he thong ban ve xe khach truc tuyen GreenBus.

Hay viet class TicketValidator bang Java de kiem tra ma ve hop le theo cac quy tac:
1. Ma ve bat buoc bat dau bang tien to "BUS-".
2. Tiep theo la ma tinh/thanh pho gom dung 2 ky tu chu in hoa A-Z, vi du HN, SG, DN.
3. Tiep theo la dau gach ngang "-".
4. Tiep theo la 6 chu so bieu dien ngay di xe theo dinh dang YYMMDD.
5. Ngay di xe khong duoc la ngay trong qua khu, phai lon hon hoac bang ngay hien tai cua he thong.

Yeu cau xu ly loi bien:
1. Neu ma ve null thi tra ve false, khong duoc gay NullPointerException.
2. Neu ma ve rong hoac chi gom khoang trang thi tra ve false.
3. Neu sai dinh dang thi tra ve false.
4. Neu ngay thang khong hop le, vi du ngay 32 thang 13 hoac ngay 30 thang 2, thi tra ve false.
5. Neu ngay di xe trong qua khu thi tra ve false.
6. Chi dung thu vien chuan Java.

Hay tra ve:
1. Ma nguon Java hoan chinh cho class TicketValidator.
2. Mot ham main nho de chay thu vai truong hop hop le va khong hop le.
3. Giai thich ngan gon cach code xu ly tung nhom quy tac.

AI:
Duoi day la phien ban TicketValidator dau tien:

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

Code tren dung regex de kiem tra dinh dang co ban, cat 6 chu so ngay thang, chuyen thanh nam 20YY va so sanh voi LocalDate.now().

Nguoi dung:
Ban la mot ky su kiem thu va kiem toan bao mat doc lap. Hay review ma nguon Java TicketValidator duoi day nhu review truoc khi dua vao he thong GreenBus.

Muc tieu review:
1. Tim loi logic tiem an trong xu ly dinh dang BUS-XX-YYMMDD.
2. Kiem tra nguy co NullPointerException, StringIndexOutOfBoundsException hoac DateTimeException.
3. Kiem tra cach phan tich ngay thang co chap nhan ngay khong hop le hay khong, vi du 260230 hoac 261332.
4. Kiem tra logic so sanh voi ngay hien tai co gap van de khi chuyen ngay, chuyen nam hoac khi he thong chay o mui gio khac hay khong.
5. Kiem tra ma co de test tu dong hay khong. Neu phu thuoc truc tiep vao LocalDate.now() thi hay de xuat cach cai tien bang Clock.
6. Dua ra danh sach loi/rui ro theo muc do nghiem trong.
7. Viet lai phien ban TicketValidator toi uu cuoi cung, su dung java.time.LocalDate hien dai va co kha nang test on dinh.

Ma nguon can review:
[Ma nguon TicketValidator phien ban dau tien o tren]

AI:
Ket qua review doc lap:

1. Muc do cao: DateTimeFormatter.ofPattern("yyyyMMdd") mac dinh co ResolverStyle SMART. Cach parse nay co the khong chan nghiem ngat mot so ngay khong hop le nhu 20260230 theo dung ky vong nghiep vu. Nen dung parse nghiem ngat hoac tach ngay thang roi tao LocalDate.of de DateTimeException duoc xu ly ro rang.
2. Muc do cao: Code khong bat DateTimeException. Neu chuoi co dinh dang so nhung ngay thang khong hop le, ham co the nem exception thay vi tra ve false.
3. Muc do trung binh: Code goi truc tiep LocalDate.now(), lam test phu thuoc ngay thuc te cua may chay. Nen inject Clock de co the test cac truong hop qua khu, hien tai va tuong lai on dinh.
4. Muc do trung binh: LocalDate.now() dung mui gio mac dinh cua he thong. Neu server chay o mui gio khac, ket qua co the khac voi ngay nghiep vu cua GreenBus. Dung Clock giup cau hinh mui gio ro rang hon.
5. Muc do thap: Viec substring(7) hien tai an toan vi da regex truoc, nhung nen dung group regex de code ro nghiep vu hon va giam phu thuoc vao chi so cat chuoi.

Phien ban toi uu nen dung Pattern co group, Clock, LocalDate.of va bat DateTimeException de tra ve false voi ngay khong hop le.
````

## 5. Ma nguon Java toi uu cuoi cung sau kiem duyet

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

## 6. Giai thich phien ban cuoi

Phien ban cuoi dung regex `^BUS-([A-Z]{2})-(\\d{6})$` de tach ro ma tinh va ngay di xe. Ma tinh bat buoc la 2 chu cai in hoa A-Z. Phan ngay bat buoc la 6 chu so.

Ham `parseTravelDate` tach `YY`, `MM`, `DD`, chuyen nam thanh `2000 + YY`, sau do dung `LocalDate.of(year, month, day)`. Neu ngay thang khong hop le, Java nem `DateTimeException` va ham `isValid` tra ve `false`.

Class nhan `Clock` qua constructor de co the test on dinh theo ngay gia lap, dong thoi van co constructor mac dinh dung `Clock.systemDefaultZone()` khi chay thuc te. Viec so sanh ngay dung `travelDate.isBefore(today)`, nen ma ve cua hom nay va tuong lai hop le, con ngay qua khu khong hop le.
