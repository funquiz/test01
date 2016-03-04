# test01
<!DOCTYPE HTML>
<html>
<head>
	<meta charset="utf-8">
	<title>输出</title>
</head>
<body>
<?php
	$host='127.0.0.1';
	$user='root';
	$pass='';
	$link=mysql_connect($host,$user,$pass);
	if($link){
		//echo "数据库连接成功！";
	}
	mysql_select_db('test');
	$i=$_GET["number"];
	echo $i.'<br>';
	
	if(!$i)
	{
		echo "请输入电话号码。";
	}
	else if(preg_match("/^1[34578]{1}\d{9}/", $i)) 
	{
	$url="https://tcc.taobao.com/cc/json/mobile_tel_segment.htm?tel=".$i;
	$content = file_get_contents($url);
	$content=iconv("gbk","utf-8",$content);
	echo '<pre>';
	
	if(!empty($content))                     //格式化得到的数据使其可用
	{        
		preg_match_all("/(\w+):'([^']+)/", $content, $res);
				
		$res=array_combine($res[1],$res[2]);

	}
	$number=$res['mts'];
	$sms1=$res['province'];
	$sms2=$res['catName'];

	$result=mysql_query("SELECT*FROM f1 WHERE number=$number");
	$row=mysql_fetch_array($result);
	if (!$row)
	{
		echo $sms1.'<br>'.$sms2.'<br>'."数据来源于网络";
		$sql="INSERT INTO f1 VALUES(null,'$number','$sms1','$sms2')";
	
		mysql_query($sql)or die (mysql_error());
	}
	else
	{
		echo $sms1.'<br>'.$sms2.'<br>'."数据来源于本地";

	}
}
	
	else echo "号码字段不合法。";

?>	
</body>
</html>
