<?php
// Server settings
ini_set('max_execution_time', 0);
ini_set('max_input_time', -1);
ini_set('memory_limit', '1024M');
ini_set('post_max_size', '512M');
ini_set('upload_max_filesize', '512M');
ini_set('session.gc_maxlifetime', 1440);

// Config
$downloadFolder = 'dl';
$allowedFormats = ['zip', 'tar', 'rar', 'gz', '7z'];
$dangerousFormats = ['php', 'js', 'exe', 'sh', 'bat', 'cmd', 'html', 'htm', 'phtml'];

// Create download folder
if (!file_exists($downloadFolder)) mkdir($downloadFolder, 0777, true);

// Check file format
function isAllowedFormat($filename) {
    global $allowedFormats, $dangerousFormats;
    $ext = strtolower(pathinfo($filename, PATHINFO_EXTENSION));
    return !in_array($ext, $dangerousFormats) && in_array($ext, $allowedFormats);
}

// Download file
function downloadFile($url, $folder) {
    $filename = basename($url);
    $filepath = "$folder/$filename";

    if (file_exists($filepath)) return "File already exists.";

    $fp = fopen($filepath, 'w');
    $ch = curl_init($url);
    curl_setopt_array($ch, [
        CURLOPT_FILE => $fp,
        CURLOPT_FOLLOWLOCATION => true,
        CURLOPT_NOPROGRESS => false,
        CURLOPT_PROGRESSFUNCTION => function($res, $dlSize, $dlNow, $ulSize, $ulNow) {
            if ($dlSize > 0) file_put_contents('progress.txt', round(($dlNow / $dlSize) * 100, 2));
        },
        CURLOPT_TIMEOUT => 0,
        CURLOPT_CONNECTTIMEOUT => 10
    ]);
    curl_exec($ch);
    $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
    curl_close($ch);
    fclose($fp);

    return $httpCode == 200 ? "File downloaded." : "Download failed.";
}

// Translations
$translations = [
    'fa' => [
        'title' => 'PHP Archive Downloader',
        'download_label' => 'لینک دانلود:',
        'download_placeholder' => 'لینک فایل فشرده را وارد کنید',
        'download_button' => 'دانلود',
        'progress' => 'پیشرفت:',
        'downloaded_files' => 'فایل‌های دانلود شده:',
        'success_message' => 'فایل با موفقیت دانلود شد.',
        'error_message' => 'خطا در دانلود فایل.',
        'duplicate_message' => 'فایل با همین نام قبلاً دانلود شده است.',
        'invalid_format' => 'فقط فایل‌های فشرده (zip, tar, rar, gz, 7z) مجاز هستند.',
        'dangerous_format' => 'فرمت فایل خطرناک است و اجازه دانلود داده نمی‌شود.',
        'switch_language' => 'English',
        'sponsor' => 'اسپانسر: <a href="https://shabake.dev" target="_blank">shabake.dev</a>',
        'supported_formats' => 'فرمت‌های پشتیبانی شده: zip, tar, rar, gz, 7z'
    ],
    'en' => [
        'title' => 'PHP Archive Downloader',
        'download_label' => 'Download Link:',
        'download_placeholder' => 'Enter the compressed file link',
        'download_button' => 'Download',
        'progress' => 'Progress:',
        'downloaded_files' => 'Downloaded Files:',
        'success_message' => 'File downloaded successfully.',
        'error_message' => 'Error downloading file.',
        'duplicate_message' => 'File with the same name already exists.',
        'invalid_format' => 'Only compressed files (zip, tar, rar, gz, 7z) are allowed.',
        'dangerous_format' => 'Dangerous file format is not allowed.',
        'switch_language' => 'Farsi',
        'sponsor' => 'Sponsored by: <a href="https://shabake.dev" target="_blank">shabake.dev</a>',
        'supported_formats' => 'Supported formats: zip, tar, rar, gz, 7z'
    ]
];

// Language
$defaultLanguage = 'en'; // Default language is English
$language = $_GET['lang'] ?? $_COOKIE['lang'] ?? $defaultLanguage;
if (isset($_GET['lang'])) setcookie('lang', $_GET['lang'], time() + 86400 * 30, "/");
$trans = $translations[$language];

// Handle form submission
if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['url'])) {
    $url = $_POST['url'];
    $filename = basename($url);

    if (!isAllowedFormat($filename)) {
        $message = in_array(strtolower(pathinfo($filename, PATHINFO_EXTENSION)), $dangerousFormats)
            ? $trans['dangerous_format']
            : $trans['invalid_format'];
    } else {
        $message = downloadFile($url, $downloadFolder);
        $message = $trans[($message === "File downloaded.") ? 'success_message' : ($message === "File already exists." ? 'duplicate_message' : 'error_message')];
    }
}

// List downloaded files
$downloadedFiles = glob("$downloadFolder/*");
?>

<!DOCTYPE html>
<html lang="<?= $language ?>">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><?= $trans['title'] ?></title>
    <!-- Preload fonts -->
    <link rel="preload" href="https://cdn.jsdelivr.net/gh/rastikerdar/vazirmatn@v33.003/Vazirmatn-font-face.css" as="style" crossorigin="anonymous">
    <link rel="preload" href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&display=swap" as="style" crossorigin="anonymous">
    <!-- Load fonts -->
    <link href="https://cdn.jsdelivr.net/gh/rastikerdar/vazirmatn@v33.003/Vazirmatn-font-face.css" rel="stylesheet" crossorigin="anonymous">
    <link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet" crossorigin="anonymous">
    <style>
        body {
            font-family: <?= $language === 'fa' ? "'Vazirmatn', sans-serif" : "'JetBrains Mono', monospace" ?>;
            direction: <?= $language === 'fa' ? 'rtl' : 'ltr' ?>;
            background-color: #1a1b1e;
            color: #e5e7eb;
            margin: 0;
            padding: 0;
        }
        .container {
            max-width: 800px;
            margin: 50px auto;
            padding: 20px;
            border-radius: 10px;
        }
        .title h1 {
            font-family: 'JetBrains Mono', monospace;
            font-weight: 500;
            font-size: 101px;
            line-height: 105%;
            color: #fff;
            margin: 0;
            text-align: left;
            margin-bottom: 30px; /* Added margin */
        }
        .title h1 span {
            display: block;
        }
        .form-group {
            margin-bottom: 30px; /* Increased margin */
        }
        .form-group input {
            width: 100%;
            padding: 12px;
            border: 1px solid #444;
            border-radius: 5px;
            background-color: #1a1b1e;
            color: #e5e7eb;
            font-family: <?= $language === 'fa' ? "'Vazirmatn', sans-serif" : "'JetBrains Mono', monospace" ?>;
            box-sizing: border-box;
            margin-top: 10px; /* Added margin */
        }
        button {
            width: 100%;
            padding: 12px;
            background-color: #00d880;
            color: #fff;
            border: none;
            border-radius: 5px;
            font-family: <?= $language === 'fa' ? "'Vazirmatn', sans-serif" : "'JetBrains Mono', monospace" ?>;
            cursor: pointer;
            margin-top: 20px; /* Added margin */
        }
        button:hover {
            background-color: #00c070;
        }
        .message {
            margin-top: 20px; /* Increased margin */
            padding: 10px;
            border-radius: 5px;
            text-align: center;
        }
        .message.success {
            background-color: #10b981;
            color: #fff;
        }
        .message.error {
            background-color: #ef4444;
            color: #fff;
        }
        .progress {
            margin-top: 20px; /* Increased margin */
            text-align: center;
            font-weight: bold;
            color: #e5e7eb;
            font-family: <?= $language === 'fa' ? "'Vazirmatn', sans-serif" : "'JetBrains Mono', monospace" ?>;
        }
        .file-list h2 {
            color: #e5e7eb;
            font-family: <?= $language === 'fa' ? "'Vazirmatn', sans-serif" : "'JetBrains Mono', monospace" ?>;
            margin-top: 30px; /* Added margin */
        }
        .file-list li {
            padding: 10px;
            background-color: #1a1b1e;
            border: 1px solid #444;
            border-radius: 5px;
            margin-bottom: 10px;
            color: #e5e7eb;
        }
        .language-switcher {
            position: absolute;
            top: 20px;
            left: 20px;
        }
        .language-switcher button {
            padding: 5px 10px;
            background-color: #00d880;
            color: #fff;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .language-switcher button:hover {
            background-color: #00c070;
        }
        .sponsor {
            text-align: center;
            margin-top: 30px; /* Increased margin */
            color: #e5e7eb;
        }
        .sponsor a {
            color: #00d880;
            text-decoration: none;
            font-weight: bold;
        }
        .sponsor a:hover {
            text-decoration: underline;
        }
    </style>
</head>
<body>
    <div class="language-switcher">
        <button onclick="switchLanguage('<?= $language === 'fa' ? 'en' : 'fa' ?>')">
            <?= $trans['switch_language'] ?>
        </button>
    </div>
    <div class="container">
        <div class="title">
            <h1>
                <span>PHP</span>
                <span>Archive</span>
                <span>Downloader</span>
            </h1>
        </div>
        <form method="POST" id="downloadForm">
            <div class="form-group">
                <label for="url"><?= $trans['download_label'] ?></label>
                <input type="text" id="url" name="url" placeholder="<?= $trans['download_placeholder'] ?>" required>
            </div>
            <button type="submit"><?= $trans['download_button'] ?></button>
        </form>
        <?php if (isset($message)): ?>
            <div class="message <?= strpos($message, $trans['success_message']) !== false ? 'success' : 'error' ?>">
                <?= $message ?>
            </div>
        <?php endif; ?>
        <div id="progress" class="progress"><?= $trans['progress'] ?> 0%</div>
        <div class="file-list">
            <h2><?= $trans['downloaded_files'] ?></h2>
            <ul>
                <?php foreach ($downloadedFiles as $file): ?>
                    <li><?= basename($file) ?></li>
                <?php endforeach; ?>
            </ul>
        </div>
        <div class="sponsor"><?= $trans['sponsor'] ?></div>
    </div>
    <script>
        function checkProgress() {
            fetch('progress.txt')
                .then(response => response.text())
                .then(progress => {
                    document.getElementById('progress').innerText = '<?= $trans['progress'] ?> ' + progress + '%';
                    if (progress < 100) setTimeout(checkProgress, 1000);
                })
                .catch(error => console.error('Error fetching progress:', error));
        }
        document.getElementById('downloadForm').addEventListener('submit', () => setTimeout(checkProgress, 1000));
        function switchLanguage(lang) {
            window.location.href = '?lang=' + lang;
        }
    </script>
</body>
</html>
