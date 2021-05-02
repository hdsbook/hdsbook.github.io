# php 實作匯出 .ics 檔並解決時區問題


<!--more-->

在此奉上簡單的 ics 匯出實作程式

```php
<?php

namespace Hdsbook\ICS;                  // 這裡請替換掉你自己要用的 namespace

class ICS
{
    public $data = "";
    public $start =
        "BEGIN:VCALENDAR\n" .
        "PRODID:-//Hdsbook Corporation//Test System//EN\n" .  // 此行中間內容可自行替換，這行不加也行
        "VERSION:2.0\n" .
        "CALSCALE:GREGORIAN\n" .       // 曆法：陽曆
        "METHOD:REQUEST\n";            // 動作：新增、修改

    public $timezone =
        "BEGIN:VTIMEZONE\n\n" .
            "TZID:Asia/Taipei\n\n" .
            "X-LIC-LOCATION:Asia/Taipei\n\n" .
            "BEGIN:STANDARD\n\n" .
                "TZOFFSETFROM:+0800\n\n" .
                "TZOFFSETTO:+0800\n\n" .
                "TZNAME:CST\n\n" .
                "DTSTART:19700101T000000\n\n" .
            "END:STANDARD\n\n" .
        "END:VTIMEZONE\n" ;

    public $end = "END:VCALENDAR\n";

    public function __construct()
    {

    }

    /**
     * add event
     *
     * @param string $start
     * @param string $end
     * @param string $name
     * @param string $description
     * @param string $location
     * @param string $uid 設定該活動的唯一識別ID，用於針對該活動之後進行修改
     * @return string
     */
    public function add(
        $start,
        $end,
        $name,
        $description,
        $location,
        $uid = null
    ) {
        // 公私  (CLASS): PUBLIC / PRIVATE
        // 日期  (DTSTART/DTEND)
        // 地點  (LOCATION)
        $this->data .=
        "BEGIN:VEVENT\n" .
            "DTSTART;TZID=Asia/Taipei:" . date("Ymd\THis\Z", strtotime($start)) . "\n" .
            "DTEND;TZID=Asia/Taipei:" . date("Ymd\THis\Z", strtotime($end)) . "\n" .
            "LOCATION:" . $location . "\n" .
            "TRANSP: OPAQUE\n" .
            "SEQUENCE:0\n" .
            "UID:" . ($uid ?? '') . "\n" .
            "DTSTAMP:" . date("Ymd\THis\Z") . "\n" .
            "SUMMARY:" . $name . "\n" .
            "DESCRIPTION:" . $description . "\n" .
            "PRIORITY:1\n" .
            "CLASS:PUBLIC\n" .
            // "BEGIN:VALARM\n" .
            //     "TRIGGER:-PT1D\n" .
            //     "ACTION:DISPLAY\n" .
            //     "DESCRIPTION:Reminder\n" .
            // "END:VALARM\n" .
        "END:VEVENT\n";
    }

    /**
     * 將檔案存到 server
     *
     * @param string $file_path
     */
    public function save($file_path)
    {
        file_put_contents($file_path, $this->getData());
    }

    /**
     * 下載檔案
     *
     * @param string $file_name  檔案名稱，不用含 .ics 的副檔名
     */
    public function download($file_name)
    {
        header("Content-type:text/calendar");
        header('Content-Disposition: attachment; filename="' . $file_name . '.ics"');
        Header('Content-Length: '.strlen($this->getData()));
        Header('Connection: close');
        echo $this->getData();
    }

    /**
     * 取得檔案內容
     *
     * @return string
     */
    public function getData()
    {
        return $this->start . $this->timezone . $this->data . $this->end;
    }
}

```

## **使用方式**

```php
<?php
use Hdsbook\ICS\ICS;
// ...
$ics = new ICS();
foreach ($events as $evt) {
    $ics->add(
        $evt['start'],
        $evt['end'],
        $evt['title'],
        $evt['description'],
        $evt['location'],
        $evt['uid']
    );
}
$ics->download("行事歷匯出檔名");
```

## **提醒設定**

```php
// 10080 分鐘前提醒 (一周前提醒)
BEGIN:VALARM
TRIGGER:-PT10080M
ACTION:DISPLAY
DESCRIPTION:Reminder
END:VALARM

// 一天前提醒
TRIGGER:-PT1D

// 若拿掉這幾行(BEGIN:VALARM ~ END:VALARM)，則匯入時會預先抓取google帳號所設定的預設提醒時
```

## **疑難排解**

解決匯入後時間往後延了八小時的問題 (原因與台灣時區有關係)

```php
// 一般教學文件只會寫
DTSTART: . date("Ymd\\THis\\Z", strtotime($start)) . "\\n" .
DTEND: . date("Ymd\\THis\\Z", strtotime($end)) . "\\n" .

// 改為
DTSTART;TZID=Asia/Taipei: . date("Ymd\\THis\\Z", strtotime($start)) . "\\n" .
DTEND;TZID=Asia/Taipei: . date("Ymd\\THis\\Z", strtotime($end)) . "\\n" .
```

## **參考**

[iCal Applicatoin - iCal Java API 研究與教學 - Soul & Shell Blog](https://blog.toright.com/posts/198/ical-java-api-%E7%A0%94%E7%A9%B6%E8%88%87%E6%95%99%E5%AD%B8.html)

[MRBS 會議室預約管理系統 之 iCalendar檔案 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/questions/10114708)

[ICS timezone not working](https://stackoverflow.com/questions/7626114/ics-timezone-not-working)

