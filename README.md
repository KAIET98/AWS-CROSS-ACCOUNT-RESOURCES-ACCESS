# AWS-CROSS-ACCOUNT-RESOURCES-ACCESS
Repositorio con explicaciones de como acceder a recursos de otros accounts.


En este repositorio vamos a implementar la funcionalidad de acceso de un recurso de la subcuenta A, desde la subcuenta B dentro de la cuenta matriz. Dicho de otra manera: vamos a tener la siguiente estructura: 

Cuenta maestra:

1- Subcuenta A: donde estará el recurso
2- Subcuenta B: la cuenta desde la cual queremos acceder al recurso de la subcuenta A. 

## Creación de usuarios: 

Nos posicionamos en la cuenta matriz, y creamos los siguientes dos usuarios: 

1. Nerea: (Cuenta A)

En tipos de credenciales le ofreceremos ambas; es decir, 'Access key' y también el Password. 

En el apartado de Console Password desplegaremos lo siguiente: 

```
nereatucontraseña2002!
```
Permissions: 

Vamos a ofrecerle la posibilidad de ver los Buckets de S3, es decir, permisos para todo. "Attach existing policies directly" > filtramos por: "s3", y elegimos "AmazonS3FullAccess".

Finalmente le damos a crear usuario. 

Copiamos el ARN dle usuario y lo copiamos en un block de notas: 

```
arn:aws:iam::CODIGO-IDENTIFICATIVO:user/Nerea
```

2. Mikel (Cuenta B)

Vamos a la consola de IAM otra vez y replicamos los pasos anteriores. 

En tipos de credenciales le ofreceremos ambas; es decir, 'Access key' y también el Password. 

En el apartado de Console Password desplegaremos lo siguiente: 

```
Mikeltucontraseña2002!
```
Permissions: 

Vamos a ofrecerle la posibilidad de ver los Buckets de S3, es decir, permisos para todo. "Attach existing policies directly" > filtramos por: "s3", y elegimos "AmazonS3FullAccess" y "AdministratorAccess".

Finalmente le damos a crear usuario. 

Nos vamos al usuario que acabamos de crear y copiamos su ARN y lo pegamos en un block de notas.

```
arn:aws:iam::CODIGO-IDENTIFICATIVO:user/Mikel
```

## Creación de bucket

Salimos de la cuenta matriz, y entramos en la cuenta de Nerea, lo primero nos va a pedir que cambiemos la contraseña, establecemos una nueva

```
nereatucontraseña2003!
```

Vamos a los Buckets de S3 y creamos uno: 

```
bucket-de-nerea
```
Creamos el bucket. 

Clickamos en el y nos posicionamos en "Properties" y guardamos en el block de notas el arn: 

```
arn:aws:s3:::bucket-de-nerea
```

Luego vamos a Permissions">"Bucket policy">"Edit"; y escribimos algo parecido a esto:

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			
			"Principal": {
			    
			    "AWS": "arn:aws:iam::CODIGO-IDENTIFICATIVO:user/Mikel"
			},
			"Effect": "Allow",
			"Action": [
			    "s3:GetObject", 
			    "s3:PutObject",
			    "s3:PutObjectAcl"
			    
			    
			    ],
			"Resource": "arn:aws:s3:::bucket-de-nerea/*"
		}
	]
}
```
## Delegacion de permisos

Ahora bien, a la cuenta B se le ha dado acceso al bucket, pero ahora se le tienen que dar permisos como editarlo. Entramos ne la cuenta de  Mikel. Igual que en el caso de Nerea, primeor nos va a pedir que cambiemos la contraseña.


```
Mikeltucontraseña2003!
```

Pero claro, no le hemos dado permisoso para editar sus roles; por loq ue tendremos que entrar a la cuenta maestra. Entramos a IAM, al perfil de Mikel y le damos "Add Permissions">"Attach existing policies directly">"Create policy".


```
{
    "Version": "2012-10-17",
    "Statement": [
        
        {
            "Effect": "Allow", 
            "Action": [
                "s3:GetObject", 
                "s3:PutObject", 
                "s3:PutObjectAcl"
                
                
                
                ], 
                "Resource": "arn:aws:s3:::bucket-de-nerea/*"

        }
      
        ]
}
```


Y le ponemos como nombre: 



```
Acceso_bucket_nerea
```

Con eso si nos posicionamos ya en S3, deberiamos de ver el Bucket de Nerea :).
