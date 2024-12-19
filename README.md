# BetterFilePickerInMAUI
BetterFilePickerInMAUI based on MKFilePicker by mingkly.

BetterFilePickerInMAUI是mingkly的MKFilePicker的小改版。

Only support Android and Windows.

仅支持Android和Windows

Add persistent Uri grants limit in Android platform. See:https://issuetracker.google.com/issues/149315521

增加Android端持久Uri授权限制。详情请见：https://issuetracker.google.com/issues/149315521

When FilePickOptions is Null, you can select any files:

当FilePickOptions为Null时，可以选择任意文件：

	FilePickResult res = await Picker.PickFileAsync(null);

1.pick a video file and read it:

挑选文件并读取:

	FilePickResult res = await Picker.PickFileAsync(FilePickOptions.Videos);
	using var stream = Picker.OpenPickedFile(res.PlatformPath, "r");

if you target android api29 or lower and granted android.permission.READ_EXTERNAL_STORAGE and android.permission.WRITE_EXTERNAL_STORAGE,
or target android api29 higher and granted android.permission.MANAGE_EXTERNAL_STORAGE,
or target windows,you can read file like this:

如果目标安卓平台在api29以下并且获得了读写外部存储权限，或者api29以上并获得管理所有文件权限，或者windows平台，可以直接使用file api访问：

ps:i found not all of picked file can get their absloute path,it may return wrong absloute path like :video:29999(which like media uri) or something else,user can use other contentProvider so this value could be very strange.sp you can try catch its exception
and use platform path instead.

后续发现这个绝对路径返回不一定正确，可能会返回video:1111这种media Uri形式，甚至用户可以选择其他contentproiver返回更为奇怪的路径
，可以放在try catch快里捕获异常改为用platformPath
	
	using var fs=File.OpenRead(res.FullPath);

2，pick multi video Files（DO NOT use this method to select SINGLE file）:

选择多个视频文件（不能在此方法下选择单个文件）：

	var results = await Picker.PickFilesAsync(FilePickOptions.Videos);

3，pick special type file:

选择特定文件：

	var fileOptions = new FilePickOptions()
	{
		FileTypes = new FilePickerFileType(new Dictionary<DevicePlatform, IEnumerable<string>>
		{
			{DevicePlatform.Android,new string[]{"image/*"} },
			{DevicePlatform.WinUI,new string[]{"*.png", "*.jpg", "*.jpeg", "*.webp","*.gif","*.bmp"} }
		}),
	};
	FilePickResult res = await Picker.PickFileAsync(fileOptions);

  4,pick a folder and create file under it:

  挑选文件夹，并在其下创建文件：

	var folder = await Picker.PickFolderAsync(null);
	var res = Picker.CreateFile(folder.PlatformPath, "test.txt");
	using var stream=Picker.OpenPickedFile(res.PlatformPath, "w");
	using var sw=new StreamWriter(stream);
	sw.Write("测试文字");

  5，pick a folder and create folder under it:

  挑选文件夹并在其下创建文件夹：

	var res3 = Picker.CreateFolder(folder.PlatformPath, "testFolder");
	var res4 = Picker.CreateFile(res3.PlatformPath, "TestInnerFolder/test.txt");
	using var stream = Picker.OpenPickedFile(res4.PlatformPath, "w");
	using var sw = new StreamWriter(stream);
	sw.Write("测试文字");
 