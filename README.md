nas
===

formulario
#! / Usr / bin / php
<? php
definir ( 'BASE_DIR' ,  dirname ( __FILE__ ));

/ **
 * Lista de directorios para crear
 * /
$ Dirs  =  array (
	'Lib'  =>  0750 ,
	'logs'  =>  0700 ,
	'Www'  =>  0750 ,
	'www / activos'  =>  0770 ,
	'Www / almacenamiento'  =>  0770 ,
	'Protegida'  =>  0750 ,
	"Protegido / runtime '  =>  0770 ,
);
/ **
 * Lista de los submódulos git
 * /
$ GitModules  =  array (
	'Lib / yü'  =>  array (
		'Url'  =>  'git :/ / github.com / yiisoft / yii.git' ,
		'Tag'  =>  .1.3 '1 ' ,
	),
	"Protegido / extensions / yü-localfs '  =>  'git@github.com: Mediasite / yü-LocalFS.git' ,
);
/ **
 * DB
 * /
$ Db  =  array (
	'Class'  =>  'CDbConnection' ,
	'ConnectionString'  =>  'mysql: host = 127.0.0.1; dbname = decoración' ,
	'EmulatePrepare'  =>  cierto ,
	'Username'  =>  'root' ,
	'Password'  =>  '31415 ' ,
	'Charset'  =>  'utf8' ,
	'SchemaCachingDuration'  =>  0 ,
	'EnableProfiling'  =>  cierto ,
	'EnableParamLogging'  =>  cierto ,
	'TablePrefix'  =>  '' ,
);
/ **
 * Rutas
 * /
$ Rutas  =  array (
	'IMI'  =>  'IMI' ,
	'GII / <controller:\w+>'  =>  'GII / <Controller>' ,
	'GII / <controller:\w+> / <action:\w+>'  =>  'GII / <Controller> / <acción>' ,

	'Login /'  =>  'site / login' ,
	'Logout /'  =>  'site / cierre de sesión' ,

	"Http:// $ dominio / archivo / <_a:\w+> / * "  =>  'El archivo / <_a>' ,

	''  =>  'site / index' ,
);
/ **
 * Params
 * /
$ Params  =  array (
	'Name'  =>  'Su nombre de la aplicación " ,
	'Id'  =>  'app-id' ,
	'Debug'  =>  cierto ,
	'Dominio'  =>  'localhost'
);
/ **
 * La aplicación de configuración principal
 * /
$ Config  =  array (
	'Params'  =>  '{{$ params}}' ,
	'Id'  =>  '{{$ params ["id"]}}' ,
	'Name'  =>  '{{$ params ["name"]}}' ,
	'BasePath'  =>  realpath ( __DIR__  .  '/ protegida " ),
	/ / Precarga de 'log' componente
	'Precarga'  =>  array ( 'log' ),
	Clases de modelo / / carga automática y de componentes
	'Lenguaje'  =>  'ru' ,
	"Importación"  =>  array (
		'application.models. *' ,
		'application.components. *' ,
		'application.behaviors. *' ,
	),
	'ControllerMap'  =>  array (
		'Archivo'  =>  array (
			'Class'  =>  'ext.yii-localfs.UploadController' ,
		),
	),
	'Módulos'  =>  '{{require (__DIR__. "/ modules.php")}}' ,
	'componentes'  =>  array (
		'AuthManager'  =>  array (
			'Class'  =>  'CDbAuthManager' ,
			'ConnectionID'  =>  'db' ,
		),
		'User'  =>  array (
			/ / Habilitar la autenticación basada en cookies
			'AllowAutoLogin'  =>  cierto ,
			'LoginUrl'  =>  array ( '/ account / login' ),
		),
		'UrlManager'  =>  array (
			"UrlFormat"  =>  "camino" ,
			"UrlSuffix"  =>  "/" ,
			"ShowScriptName"  =>  false ,
			"UseStrictParsing"  =>  false ,
			'Reglas'  =>  '{{include ("routes.php")}}' ,
		),
		'Db'  ​​=>  '{{include ("db.php")}}' ,
		'ErrorHandler'  =>  array (
			'ErrorAction'  =>  'site / error' ,
		),
		'Fs'  =>  array (
			'Class'  =>  'ext.yii-localfs.LocalFS' ,
			'NestedFolders'  =>  5 ,
		),
		'Cache'  =>  array (
			'Class'  =>  'CMemCache' ,
			'UseMemcached'  =>  cierto ,
			'Servidores'  =>  array (
				array (
					'Anfitrión'  =>  '127 .0.0.1 ' ,
					'Puerto'  =>  11211 ,
				),
			),
		),
	),
);

/ **
 * Inicio de la escritura
 * /
foreach  ( $ dirs  como  $ dir  =>  $ mode )  {
	$ Dir  =  __DIR__  .  '/'  .  $ dir ;
	si  ( ! is_dir ( $ dir ))  {
		si  ( ! mkdir ( $ dir ,  $ mode ))  {
			echo  "No se pudo crear directorio $ dir \ n " ;
			exit ( 0 );
		}
	}
}

si  ( ! file_exists ( __DIR__  .  '/ .git / config' ))  {
	echo  "No se han encontrado repo, inicializar nuevo repositorio git ... \ n " ;
	ejecutivo ( 'git init' ,  $ salida ,  $ code );
	InstallHelper :: processOutput ( $ salida );
	si  ( $ code  ! ==  0 )  {
		echo  "No se pudo inicializar raíz git repo. \ n " ;
		exit  ( 1 );
	}
}

$ Gitconfig  =  file_get_contents ( __DIR__  .  '/ .git / config' );
foreach  ( $ gitModules  como  $ dir  =>  $ data )  {
	$ Url  =  NULL ;
	$ Tag  =  NULL ;

	si  ( is_string ( $ data ))  {
		$ Url  =  $ data ;
	}  elseif  ( is_array ( $ datos )  &&  ! ​​empty ( $ data [ 'url' ]))  {
		$ Url  =  $ data [ 'url' ];
		si  ( isset ( $ data [ 'tag' ]))  {
			$ etiqueta  =  $ data [ 'tag' ];
		}
	}  más  {
		echo  "No se pudo entender submódulo info: \ n " ;
		var_dump ( $ data );
		echo  " \ n " ;
		exit  ( 1 );
	}

	si  ( strpos ( $ gitconfig ,  $ url )  ===  false )  {
		echo  "Añadir nuevo submódulo $ url a $ dir \ n " ;
		ejecutivo ( - 'git clone profundidad 5'  .  escapeshellarg ( $ url )  .  ''  .  escapeshellarg ( $ dir )  .  '' && submódulo git add  .  escapeshellarg ( $ url )  .  ''  .  escapeshellarg ( $ dir ),  $ salida ,  $ code );
		si  ( $ code  ! ==  0 )  {
			InstallHelper :: processOutput ( $ salida );
			echo  "No se pudo añadir submódulo $ url . \ n " ;
			exit  ( 1 );
		}
	}

	/ / La salida especificada tag
	si  ( $ tag )  {
		echo  "Pedido de etiquetas $ etiqueta por $ url \ n " ;
		chdir ( $ dir );
		ejecutivo ( 'git fetch - etiquetas - quiet && git checkout - quiet'  .  escapeshellarg ( $ tag ),  $ salida ,  $ code );
		si  ( $ code  ! ==  0 )  {
			InstallHelper :: processOutput ( $ salida );
			echo  "Error al tag de pagar $ tag para submódulo $ url . \ n " ;
			exit  ( 1 );
		}
		chdir ( BASE_DIR );
	}
}

echo  "Actualización submódulos recursivamente \ n " ;
ejecutivo ( 'update git submódulo - init - recursive' ,  $ salida ,  $ code );
si  ( $ code  ! ==  0 )  {
	InstallHelper :: processOutput ( $ salida );
	echo  "No se pudo actualizar submódulos de forma recursiva. \ n " ;
	exit  ( 1 );
}

si  ( ! is_file ( 'www / index.php' ))  {
	echo  "Creando yü webapp \ n " ;
	ejecutivo ( "eco" sí "| / lib / yü / framework / webapp yiic. '  .  __DIR__  .  '/ www git " ,  $ salida ,  $ code );
	si  ( $ code  ! ==  0 )  {
		InstallHelper :: processOutput ( $ salida );
		echo  "No se pudo crear webapp yü. \ n " ;
		exit  ( 1 );
	}
	ejecutivo ( 'www / protected / * && rm protegida www / protected-rf-cp af' ,  $ salida ,  $ code );
	si  ( $ code  ! ==  0 )  {
		InstallHelper :: processOutput ( $ salida );
		echo  "Error al mover la carpeta protegida. \ n " ;
		exit  ( 1 );
	}

	ejecutivo ( 'www mv / themes temas " ,  $ salida ,  $ code );
	si  ( $ code  ! ==  0 )  {
		InstallHelper :: processOutput ( $ salida );
		echo  "Error al mover carpeta de temas. \ n " ;
		exit  ( 1 );
	}
}


/ **
 * Generar configuraciones
 * /
file_put_contents ( 'protected / config / .gitignore " ,  "params.php \ n db.php \ n " );
$ ConfigText  =  var_export ( $ config ,  cierto );
$ ConfigText  =  "<php? \ n \ $ params = include 'params.php'; \ n volver "  .  str_replace ( array ( '\' {{' ,  '}} \'' ),  '' ,  $ configText )  .  ';' ;

InstallHelper :: createConfig ( 'main' ,  $ config ,  " \ $ params = include 'params.php'; \ n " );
InstallHelper :: createConfig ( 'consola' ,  $ config ,  " \ $ params = include 'params.php'; \ n " );
InstallHelper :: createConfig ( 'rutas' ,  $ rutas ,  " \ $ domain = \ $ params ['dominio']; \ n " );
InstallHelper :: createConfig ( 'params' ,  $ params );
InstallHelper :: createConfig ( 'params-dist' ,  $ params );
InstallHelper :: createConfig ( 'db' ,  $ db );
InstallHelper :: createConfig ( 'db-dist' ,  array ( 'password'  =>  '' ,  'username'  =>  '' )  +  $ db );
InstallHelper :: createConfig ( "módulos" );

/ **
 * Arreglo incluye
 ** /
file_put_contents ( 'protected/yiic.php' ,  str_replace ( '/../../lib/yii/' ,  '/../lib/yii/' ,  file_get_contents ( 'protected/yiic.php' )));
file_put_contents ( 'www/index.php' ,  str_replace ( "'/protected/" ,  "'/../protected/" ,  file_get_contents ( 'www/index.php' )));
file_put_contents ( 'www/index-test.php' ,  str_replace ( "'/protected/" ,  "'/../protected/" ,  file_get_contents ( 'www/index-test.php' )));

clase  InstallHelper
{
	pública  estática  función  processOutput ( $ salida )
	{
		si  ( is_array ( $ salida ))  {
			foreach  ( $ salida  como  $ string )  {
				echo  " $ string \ n " ;
			}
			volver ;
		}
		echo  ( string ) $ salida  .  " \ n " ;
	}

	pública  estática  función  createConfig ( $ name ,  $ config  =  array (),  $ pre  =  '' )
	{
		$ ConfigText  =  var_export ( $ config ,  cierto );
		$ ConfigText  =  "? <php \ n "  .  $ pre  .  "retorno"  .  str_replace ( array ( '\' {{' ,  '}} \'' ),  '' ,  $ configText )  .  ';' ;
		file_put_contents ( "protected / config / $ name php. " ,  $ configText );
	}
}
