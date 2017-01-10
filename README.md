# Запрос на получение файла со всеми портированными номерами по HTTP запросу методом GET

## Прямой запрос:

```
http://prt.incore1.ru/get/get-port.php?login=ВашЛогин&pass=ВашПарольАпи
```

Где:
* **login** - Логин в нашей системе;
* **pass** - Зашифрованный пароль полученный двойным md5.

## php запрос методом curl:

```php
<?php
$login = 'ВашЛогин';
$pass = 'ВашПароль';
$pass_md5 = md5(md5('ВашПароль')); //Шифрованный пароль используемый для API
$path_file = $_SERVER['DOCUMENT_ROOT'] . '/';
$file_zip = 'all-port.zip';
$file_csv = 'all-port.csv';
$curl = curl_init('http://prt.incore1.ru/get/get-port.php?login=' . $login . '&pass=' . $pass_md5);
$fp = fopen($path_file . $file_zip, 'w');
curl_setopt($curl, CURLOPT_FILE, $fp);
curl_setopt($curl, CURLOPT_HEADER, 0);
curl_exec($curl);
$curlinfo = curl_getInfo($curl);
curl_close($curl);
fclose($fp);
unset($fp);
if($curlinfo['content_type'] == 'application/x-zip'){
	//Разархивирование файла
	exec('cd ' . $path_file . ' && unzip ' .  $file_zip);
	//Чтение файла
	$fp = fopen($path_file . $file_csv, 'r');
	while(($data = fgetcsv($fp, 0, ',')) !== FALSE) {
		$Number = '7'.$data[0];	//Номер телефона. Подставляю, при необходимости, перед началом цифру 7, т.к. номера начинаются с 9.
		$OwnerId = $data[1]; //Оператор (mTELE2)
		$MNC = $data[2]; //код мобильной сети
		$Route = $data[3]; //Код перенаправления
		$RegionCode = $data[4]; //Код региона
		$PortDate = $data[5]; // Дата портирования
		//Далее идет Ваша работа с портированным номером
	}
	fclose($fp);
} else {
	@unlink($path_file . $file_zip); //Удаление заранее подготовленного файла для скачивания.
	echo "Ошибка скачивания файла.";
}
?>
```

В случае успеха, вы получите заархивированный файл под названием all-port.zip. В архиве находится файл - all-port.csv;
В случае неудачи, файл не будет скачан. Вероятная причина – это не правильно указан логин и пароль, либо было превышено количество скачиваний (не более 2 раз за сутки по вашему логину).
