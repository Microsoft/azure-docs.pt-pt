---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 76a080d40721fa78ad703f77f6dbe7a3363ab77e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68857464"
---
## <a name="install-wordpress"></a>Instalar o WordPress

Se quiser experimentar a sua pilha, instale uma aplicação de exemplo. Por exemplo, os seguintes passos instalam a plataforma de [WordPress](https://wordpress.org/) de código-fonte aberto para criar sites e blogues. Outras cargas de trabalho tentam incluir o [Drupal](http://www.drupal.org) e o [Moodle](https://moodle.org/). 

Esta configuração do WordPress é apenas para prova de conceito. Para instalar o WordPress mais recente na produção com as definições de segurança recomendadas, veja a [documentação do WordPress](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>Instalar o pacote do WordPress

Execute o seguinte comando:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>Configurar WordPress

Configure o WordPress para utilizar o MySQL e PHP.

No diretório de trabalho, crie um ficheiro de texto `wordpress.sql` para configurar a base de dados do MySQL para o WordPress: 

```bash
sudo sensible-editor wordpress.sql
```

Adicione os seguintes comandos, ao substituir uma palavra-passe da base de dados à sua escolha para *yourPassword* (deixe os outros valores inalterados). Se configurou anteriormente uma política de segurança do MySQL para validar a força da palavra-passe, certifique-se de que a palavra-passe cumpre os requisitos de segurança. Guarde o ficheiro.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
```

Execute o comando seguinte para criar a base de dados:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Uma vez que o ficheiro `wordpress.sql` contém credenciais de base de dados, elimine-o depois de o utilizar:

```bash
sudo rm wordpress.sql
```

Para configurar o PHP, execute o seguinte comando para abrir um editor de texto à sua escolha e crie o ficheiro `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Copie as seguintes linhas para o ficheiro, ao substituir a palavra-passe da base de dados do WordPress por *yourPassword* (deixe os outros valores inalterados). Em seguida, guarde o ficheiro.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


Mova a instalação do WordPress para a raiz do documento do servidor Web:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Agora, pode concluir a configuração do WordPress e publicar na plataforma. Abra um browser e aceda a `http://yourPublicIPAddress/wordpress`. Substitua o endereço IP público da sua VM. Deve ter um aspeto semelhante a esta imagem.

![Página de instalação do WordPress](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)
