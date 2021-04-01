---
title: Configurar a segurança para o grupo de servidores do PostgreSQL Hyperscale preparado para o Azure Arc
description: Configurar a segurança para o grupo de servidores do PostgreSQL Hyperscale preparado para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d6e27fddceb69efbb2c1697c09ee9b61d7f38ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687979"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Configurar a segurança para o grupo de servidores do PostgreSQL Hyperscale preparado para o Azure Arc

Este documento descreve vários aspetos relacionados com a segurança do seu grupo de servidores:
- Encriptação de dados inativos
- Gestão de utilizadores
   - Perspetivas gerais
   - Alterar a palavra-passe do utilizador administrativo _de postgres_
- Auditoria

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Encriptação de dados inativos
Pode implementar a encriptação em repouso, encriptando os discos nos quais armazena as suas bases de dados e/ou utilizando funções de base de dados para encriptar os dados que insere ou atualizar.

### <a name="hardware-linux-host-volume-encryption"></a>Hardware: Encriptação do volume do anfitrião Linux
Implementar encriptação de dados do sistema para proteger quaisquer dados que residam nos discos utilizados pelo seu Azure Arc ativado a configuração de Serviços de Dados. Pode ler mais sobre este tema:
- [Encriptação de dados em repouso](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) no Linux em geral 
- Encriptação de disco com o comando encriptado LUKS `cryptsetup` (Linux), https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) especificamente uma vez que o Azure Arc habilitado a Serviços de Dados funciona na infraestrutura física que fornece, está encarregue de assegurar a infraestrutura.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Software: Utilize a extensão PostgreSQL `pgcrypto` no seu grupo de servidor
Além de encriptar os discos utilizados para hospedar a sua configuração Azure Arc, pode configurar o seu grupo de servidores de hiperescala PostgreSQL ativado para expor mecanismos que as suas aplicações podem usar para encriptar dados na sua base de dados. A `pgcrypto` extensão faz parte das `contrib` extensões de Postgres e está disponível no seu grupo de servidores de hiperescala Pós-Escala Azure Arc. Encontre detalhes sobre a `pgcrypto` extensão [aqui.](https://www.postgresql.org/docs/current/pgcrypto.html)
Em resumo, com os seguintes comandos, ativa-se a extensão, cria-a e utiliza-a:


#### <a name="create-the-pgcrypto-extension"></a>Criar a `pgcrypto` extensão
Conecte-se ao seu grupo de servidor com a ferramenta cliente à sua escolha e execute a consulta padrão PostgreSQL:
```console
CREATE EXTENSION pgcrypto;
```

> Encontre detalhes [aqui](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) sobre como se conectar.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Verifique a lista das extensões prontas a utilizar no seu grupo de servidores
Pode verificar se a `pgcrypto` extensão está pronta a ser utilizada, listando as extensões disponíveis no seu grupo de servidores.
Conecte-se ao seu grupo de servidor com a ferramenta cliente à sua escolha e execute a consulta padrão PostgreSQL:
```console
select * from pg_extension;
```
Deve ver `pgcrypto` se ativou e o criou com os comandos acima indicados.

#### <a name="use-the-pgcrypto-extension"></a>Use a `pgcrypto` extensão
Agora pode ajustar o código das suas aplicações para que utilizem qualquer uma das funções oferecidas `pgcrypto` por:
- Funções gerais de hashing
- Funções de hashing de palavra-passe
- Funções de encriptação PGP
- Funções de encriptação bruta
- Funções de dados aleatórios

Por exemplo, para gerar valores de haxixe. Execute o comando:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

Devolve o seguinte haxixe:

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Ou, por exemplo:

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

devolve o seguinte haxixe:

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Ou, por exemplo, para armazenar dados encriptados como uma palavra-passe:

Vamos assumir que a minha aplicação armazena segredos na seguinte tabela:

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

E criptografo a palavra-passe deles ao criar um utilizador:

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

Vejo que a minha senha está encriptada:

```console
select * from mysecrets;
```

Resultado:

- USERid: 1
- USERname: Me
- USERpassword: $1$Uc7jzZOp$NTfcGo7F10zGOkXOwjHy31

Quando me conecto com a minha aplicação e passo uma senha, ela olhará para cima na `mysecrets` tabela e devolverá o nome do utilizador se houver uma correspondência entre a palavra-passe que é fornecida à aplicação e as palavras-passe armazenadas na tabela. Por exemplo:

- Passo a senha errada:
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Saída 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- Passo a senha correta:

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   Resultado:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

Este pequeno exemplo demonstra que pode encriptar dados em repouso (armazenar dados encriptados) em Azure Arc ativado Hyperscale postgreSQL usando a extensão Postgres `pgcrypto` e as suas aplicações podem usar funções oferecidas para `pgcrypto` manipular estes dados encriptados.

## <a name="user-management"></a>Gestão de utilizadores
### <a name="general-perspectives"></a>Perspetivas gerais
Pode utilizar a forma padrão de Postgres de criar utilizadores ou funções. No entanto, se o fizer, estes artefactos só ficarão disponíveis na função de coordenador. Durante a pré-visualização, estes utilizadores/funções ainda não conseguirão aceder aos dados distribuídos fora do nó de Coordenação e nos nós de Trabalho do grupo de servidores. O motivo é que, na pré-visualização, a definição do utilizador não é replicada para os nós de Trabalho.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>Alterar a palavra-passe do utilizador administrativo _de postgres_
A hiperescala pós-crise ativada Azure Arc vem com os _postgres_ de utilizador administrativo padrão para os quais define a palavra-passe quando cria o seu grupo de servidor.
O formato geral do comando para alterar a sua palavra-passe é:
```console
azdata arc postgres server edit --name <server group name> --admin-password
```

Onde `--admin-password` está um booleano que se relaciona com a presença de um valor na variável ambiente de **sessão** AZDATA_PASSWORD.
Se a variável ambiente de **sessão** AZDATA_PASSWORD existir e tiver um valor, executar o comando acima definirá a palavra-passe do utilizador de postgres para o valor desta variável ambiente.

Se a variável do ambiente de **sessão** AZDATA_PASSWORD existir mas não tiver valor ou a variável do ambiente de **sessão** AZDATA_PASSWORD não existir, executar o comando acima irá levar o utilizador a introduzir uma palavra-passe interativamente

#### <a name="change-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>Altere a palavra-passe do utilizador administrativo de postgres de forma interativa

1. Eliminar a variável do ambiente de **sessão** AZDATA_PASSWORD ou apagar o seu valor
2. Execute o comando:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Por exemplo
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   Será solicitado que introduza a senha e a confirme:
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   À medida que a palavra-passe está a ser atualizada, a saída do comando mostra:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="change-the-password-of-the-postgres-administrative-user-using-the-azdata_password-session-environment-variable"></a>Altere a palavra-passe do utilizador administrativo de postgres utilizando a variável ambiente de **sessão** de AZDATA_PASSWORD:
1. Desaprova o valor da variável do ambiente de **sessão** AZDATA_PASSWORD para o que pretende que seja a palavra-passe.
2. Executar o comando:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Por exemplo
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   
   À medida que a palavra-passe está a ser atualizada, a saída do comando mostra:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> Para verificar se a variável ambiental da sessão de AZDATA_PASSWORD existe e qual o valor que tem, corra:
> - Num cliente Linux:
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - Num cliente windows com PowerShell:
> ```console
> echo $env:AZDATA_PASSWORD
> ```

## <a name="audit"></a>Auditoria

Para cenários de auditoria, configuure o seu grupo de servidor para utilizar as `pgaudit` extensões de Postgres. Para mais detalhes sobre `pgaudit` o [ `pgAudit` projeto GitHub.](https://github.com/pgaudit/pgaudit/blob/master/README.md) Para ativar a `pgaudit` extensão do seu grupo de servidor, leia [as extensões PostgreSQL](using-extensions-in-postgresql-hyperscale-server-group.md).


## <a name="next-steps"></a>Passos seguintes
- Ver [ `pgcrypto` extensão](https://www.postgresql.org/docs/current/pgcrypto.html)
- Ver [utilizar extensões PostgreSQL](using-extensions-in-postgresql-hyperscale-server-group.md)

