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
$pass_md5 = md5(md5($pass)); // Шифрованный пароль используемый для API
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
		$Number = '7'.$data[0];	// Номер телефона. Подставляю, при необходимости, перед началом цифру 7, т.к. номера начинаются с 9.
		$OwnerId = $data[1]; // Оператор (mTELE2)
		$MNC = $data[2]; // код мобильной сети
		$Route = $data[3]; // Код перенаправления
		$RegionCode = $data[4]; // Код региона мобильной сети
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


# Запрос на получение списка операторов по HTTP запросу методом GET

## Прямой запрос:

```
http://prt.incore1.ru/get/operators.php?login=ВашЛогин&pass=ВашПарольАпи
```

Где:
* **login** - Логин в нашей системе;
* **pass** - Зашифрованный пароль полученный двойным md5.

## php запрос методом curl:

```php
<?php
$login = 'ВашЛогин';
$pass = 'ВашПароль';
$pass_md5 = md5(md5($pass)); //Шифрованный пароль используемый для API
$curl = curl_init('http://prt.incore1.ru/get/operators.php?login=' . $login . '&pass=' . $pass_md5);
curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
curl_setopt($curl, CURLOPT_TIMEOUT, 10);
$result = curl_exec($curl);
curl_close($curl);
$operators_array = unserialize($result);

if(is_array($operators_array["operators"])){
	foreach($operators_array["operators"] as $operator){
		$orgcode = $operator["orgcode"]; // Буквенный код оператора
		$mcc = $operator["mcc"]; // Мобильный код страны
		$mnc = $operator["mnc"]; // Код мобильной сети
		$title = $operator["title"]; // Компания
		//Далее идет Ваша работа с операторами
	}
} else {
	$error = $operators_array["error"]; // Ошибка
}
```


# Запрос на получение списка регионов по HTTP запросу методом GET

## Прямой запрос:

```
http://prt.incore1.ru/get/regions.php?login=ВашЛогин&pass=ВашПарольАпи
```

Где:
* **login** - Логин в нашей системе;
* **pass** - Зашифрованный пароль полученный двойным md5.

## php запрос методом curl:

```php
<?php
$login = 'ВашЛогин';
$pass = 'ВашПароль';
$pass_md5 = md5(md5($pass)); // Шифрованный пароль используемый для API
$curl = curl_init('http://prt.incore1.ru/get/regions.php?login=' . $login . '&pass=' . $pass_md5);
curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
curl_setopt($curl, CURLOPT_TIMEOUT, 10);
$result = curl_exec($curl);
curl_close($curl);
$regions_array = unserialize($result);

if(is_array($regions_array["regions"])){
	foreach($regions_array["regions"] as $region){
		$regioncode = $region["regioncode"]; // Код региона мобильной сети
		$title = $region["title"]; // Название региона
		$utc = $region["utc"]; // Часовой пояс региона по UTC
		$numbersubject = $region["numbersubject"]; // Код субъекта РФ
		$iso3166 = $region["iso3166"]; // Код по ISO 3166-2	
		$tz = $region["tz"]; // Time Zone
		//Далее идет Ваша работа с регионами
	}
} else {
	$error = $regions_array["error"]; // Ошибка
}
```


# Запрос на получение списка Деф-кодов по HTTP запросу методом GET

## Прямой запрос:

```
http://prt.incore1.ru/get/update-def-timezone.php?login=ВашЛогин&pass=ВашПарольАпи
```

Где:
* **login** - Логин в нашей системе;
* **pass** - Зашифрованный пароль полученный двойным md5.

## php запрос методом curl:

```php
<?php
$login = 'ВашЛогин';
$pass = 'ВашПароль';
$pass_md5 = md5(md5($pass)); //Шифрованный пароль используемый для API
$curl = curl_init('http://prt.incore1.ru/get/update-def-timezone.php?login=' . $login . '&pass=' . $pass_md5);
curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
curl_setopt($curl, CURLOPT_TIMEOUT, 10);
$result = curl_exec($curl);
curl_close($curl);
$def_array = unserialize($result);

if(is_array($def_array["phone_code_array"])){
	foreach($def_array["phone_code_array"] as $def){
		$numberfrom = $region["numberfrom"]; // Начиная номером телефона
		$numberto = $region["numberto"]; // Заканчивая номером телефона
		$regioncode = $region["regioncode"]; // Код региона мобильной сети
		$utc = $region["utc"]; // Часовой пояс региона по UTC
		$title = $region["title"]; // Название региона
		$mcc = $operator["mcc"]; // Мобильный код страны
		$mnc = $operator["mnc"]; // Код мобильной сети
		$numbersubject = $region["numbersubject"]; // Код субъекта РФ
		//Далее идет Ваша работа с деф-кодами
	}
} else {
	$error = $def_array["error"]; // Ошибка
}
```
