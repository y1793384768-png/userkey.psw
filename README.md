# <span style="color:#2E86AB;">userkey.psw</span>
<span style="color:#A23B72; font-weight:bold;">密码重置盘密钥userkey.psw</span>

# <span style="color:#F18F01;">Windows 密码重置盘 userkey.psw 原理详解</span>

这真是一个非常敏锐且有趣的发现！这其实揭开了 Windows 官方「密码重置盘」的一个核心秘密——它本质是
<span style="color:#C73E1D; font-weight:bold;">密钥验证</span>而非权限破解。

## <span style="color:#005F73;">1. 核心原理：这不是密码，是「解密密钥」</span>

<span style="color:#001219; background:#E9F7F7; padding:2px 4px;">
<code style="color:#005F73;">userkey.psw</code></span> 里面并没有存储你的明文密码，也没有存储 SAM 文件里的哈希值。

它存储的是一个用于解密你的用户账户主密钥（Master Key）的公钥/私钥对。

- <span style="color:#007F5F;">创建时：</span>Windows 生成一对密钥，公钥留在系统里，私钥经过加密后写入这个文件。
- <span style="color:#9B2226;">重置时：</span>Windows 读取 U 盘里的这个文件，用它去解开系统里的加密数据，从而获得修改密码的权限。

你的操作之所以有效，是因为你完整复制了这把“钥匙”。只要文件内容（密钥数据）没被篡改，后缀名正确，Windows 就会认可它。

## <span style="color:#005F73;">2. 为什么全是乱码？（编码与格式原理）</span>

你看到的乱码内容，原因有二：

1. <span style="color:#3F37C9; font-weight:bold;">二进制文件文本化</span>：<code>psw</code> 本质是一个二进制文件。当你用记事本打开时，记事本强行把二进制数据按 ASCII 或 UTF-8 编码去解析，不可打印的字符就变成了问号、方块或乱码。
2. <span style="color:#3F37C9; font-weight:bold;">无损复制</span>：你在记事本里“全选复制”再“粘贴”，在 Windows 的剪贴板机制下，这些二进制数据被完整保留。虽然肉眼看是乱码，但电脑存储的是完整的密钥数据流。

<span style="color:#C73E1D; font-weight:bold;">结论：</span>这个文件不包含任何可执行代码，只是一堆加密的密钥数据。所以它才可以如此“朴素”地通过重命名来创建。

---

# <span style="color:#F18F01;">总结</span>

这个操作有效的原理是：

Windows 官方密码重置盘的核心是
<span style="color:#007F5F; font-weight:bold;">userkey.psw 中的加密密钥数据</span>，
而非文件的创建方式。该文件本质是二进制密钥载体，用记事本复制粘贴仅做了一次
<span style="color:#9B2226; font-weight:bold;">二进制数据的无损搬家</span>。
只要密钥数据完整、后缀名正确，Windows 就会通过验证，允许重置密码。

---

## <span style="color:#9B2226; background:#FFECEC; padding:2px 6px;">⚠️ 重要提醒（安全边界）</span>

虽然这个方法能“复制”重置盘，但它有一个绝对的前提：

<span style="color:#C73E1D; font-weight:bold; font-size:16px;">必须已经拥有该电脑的管理员权限，才能导出这个文件。</span>

这和 Linux 破解 SAM 文件有本质区别：

1. <span style="color:#007F5F;">本方法：</span>属于「本人备份钥匙」，是合法授权操作。
2. <span style="color:#9B2226;">Linux 破解：</span>属于「撬锁进门」，是强制绕过系统验证。

所以，这个发现完美诠释了“官方工具”的设计逻辑——
<span style="color:#2E86AB; font-weight:bold; font-size:17px;">认钥不认人，认数不认创建工具</span>
