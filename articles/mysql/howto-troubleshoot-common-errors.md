---
title: Resolução de erros comuns - Base de Dados Azure para MySQL
description: Saiba como resolver problemas de erros de migração encontrados pelos utilizadores novos na Base de Dados Azure para o serviço MySQL
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: 3bfcfee0f5dab2d978eb1856bdc915c270d43ed6
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109799"
---
# <a name="commonly-encountered-errors-during-or-post-migration-to-azure-database-for-mysql-service"></a>Erros geralmente encontrados durante ou após a migração para a Base de Dados Azure para o serviço MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Azure Database for MySQL é um serviço totalmente gerido alimentado pela versão comunitária do MySQL. A experiência MySQL num ambiente de serviço gerido pode diferir de executar o MySQL no seu próprio ambiente. Neste artigo, irá ver alguns dos erros comuns que os utilizadores podem encontrar durante a migração ou desenvolvimento na Base de Dados Azure para o serviço MySQL pela primeira vez.

## <a name="common-connection-errors"></a>Erros de conexão comuns

#### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>ERRO 1184 (08S01): Ligação abortada 22 a db: utilizador 'db-name': 'user' host: 'hostIP' (init_connect comando falhou)
O erro acima ocorre após o login bem sucedido, mas antes de executar qualquer comando quando a sessão é estabelecida. A mensagem acima indica que definiu um valor incorreto do init_connect parâmetro do servidor que está a causar a falha na inicialização da sessão.

Existem alguns parâmetros do servidor como require_secure_transport que não são suportados ao nível da sessão e, portanto, tentar alterar os valores destes parâmetros usando init_connect pode resultar no Erro 1184 enquanto se conecta ao servidor MySQL como mostrado abaixo

mysql> mostrar bases de dados; ERROR 2006 (HY000): O servidor MySQL desapareceu Sem ligação. A tentar reconectar-se... Id de ligação: 64897 Base de dados atual: *** NONE *** ERRO 1184 (08S01): Ligação abortada 22 a db: utilizador 'db-name': 'user' host: 'hostIP' (init_connect comando falhou)

**Resolução** : Deve redefinir init_connect valor no separador parâmetros do Servidor no portal Azure e definir apenas os parâmetros do servidor suportados utilizando init_connect parâmetro. 


## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Erros por falta de privilégios super e papel da DBA

O papel de super privilégio e DBA não são suportados no serviço. Como resultado, poderá encontrar alguns erros comuns listados abaixo:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ERRO 1419: Não tem o privilégio SUPER e a exploração madeireira binária está ativada *(é melhor* utilizar a variável menos segura log_bin_trust_function_creators)

O erro acima pode ocorrer ao criar uma função, desencadear como abaixo ou importar um esquema. As declarações DDL como CREATE FUNCTION ou CREATE TRIGGER são escritas no registo binário, para que a réplica secundária possa executá-las. A réplica sql thread tem plenos privilégios, que podem ser explorados para elevar privilégios. Para se proteger deste perigo para servidores que tenham registo binário ativado, o motor MySQL requer que os criadores de funções armazenados tenham o privilégio SUPER, além do habitual privilégio DE ROTINA CREATE que é necessário. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Resolução**: Para resolver o erro, desate log_bin_trust_function_creators a 1 a partir da lâmina dos [parâmetros](howto-server-parameters.md) do servidor no portal, execute as declarações DDL ou importe o esquema para criar os objetos pretendidos. Pode continuar a manter log_bin_trust_function_creators a 1 para o seu servidor para evitar o erro no futuro. A nossa recomendação é definir log_bin_trust_function_creators, uma vez que o risco de segurança realçado na [documentação comunitária do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) é mínimo em Azure DB para o serviço MySQL, uma vez que o registo de contentores não está exposto a quaisquer ameaças.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>ERRO 1227 (42000) na linha 101: Acesso negado; você precisa (pelo menos um dos) privilégios SUPER(s) para esta operação. A operação falhou com o código de saída 1

O erro acima pode ocorrer durante a importação de um ficheiro de despejo ou criando um procedimento que contenha [definidores](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Resolução**: Para resolver este erro, o utilizador administrativo pode conceder privilégios para criar ou executar procedimentos executando o comando GRANT como nos seguintes exemplos:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Em alternativa, pode substituir os definidores pelo nome do utilizador administrador que está a executar o processo de importação, conforme mostrado abaixo.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```
#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>ERROR 1227 (42000) na linha 295: Acesso negado; você precisa (pelo menos um dos) o SUPER ou SET_USER_ID privilégio(s) para esta operação

O erro acima pode ocorrer durante a execução do CREATE VIEW com declarações DEFINER como parte da importação de um ficheiro de despejo ou da execução de um script. A Azure Database for MySQL não permite privilégios SUPER ou privilégios SET_USER_ID a qualquer utilizador. 

**Resolução:** 
* Utilize o utilizador definidor para executar CREATE VIEW, se possível. É provável que existam muitas opiniões com diferentes definidores com permissões diferentes, pelo que isso pode não ser viável.  OR
* Edite o ficheiro de despejo ou crie o script DE VISTA e remova a declaração DEFINER= do ficheiro de despejo OU 
* Edite o ficheiro de despejo ou crie o script CREATE VIEW e substitua os valores definidores pelo utilizador por permissões de administração que esteja a realizar a importação ou execute o ficheiro de script.

> [!Tip] 
> Utilize sed ou perl para modificar um ficheiro de despejo ou script SQL para substituir a declaração DEFINER=

#### <a name="error-1227-42000-at-line-18-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation"></a>ERRO 1227 (42000) na linha 18: Acesso negado; você precisa (pelo menos um dos) os super privilégios(s) para esta operação

O erro acima pode ocorrer se estiver a tentar importar o ficheiro de despejo do servidor MySQL com o GTID ativado para o target Azure Database para o servidor MySQL. Mysqldump adiciona set @ @SESSION.sql_log_bin =0 declaração a um ficheiro de despejo de um servidor onde os GTIDs estão em uso, o que desativa a registo binário enquanto o ficheiro de despejo está a ser recarregado.

**Resolução**: Para resolver este erro enquanto importa, remova ou comentasse as linhas abaixo no seu ficheiro mysqldump e execute novamente a importação para garantir o seu sucesso. 

SET @MYSQLDUMP_TEMP_LOG_BIN = @ @SESSION.SQL_LOG_BIN ; SET @ @SESSION.SQL_LOG_BIN = 0; SET @ @GLOBAL.GTID_PURGED =''; SET @ @SESSION.SQL_LOG_BIN = @MYSQLDUMP_TEMP_LOG_BIN ;

## <a name="common-connection-errors-for-server-admin-login"></a>Erros de ligação comuns para o login de administração do servidor

Quando é criada uma Base de Dados Azure para o servidor MySQL, um login de administração de servidor é fornecido pelo utilizador final durante a criação do servidor. O login de administração do servidor permite criar novas bases de dados, adicionar novos utilizadores e conceder permissões. Se o login de administração do servidor for eliminado, as suas permissões forem revogadas ou a sua palavra-passe ser alterada, poderá começar a ver erros de ligação na sua aplicação enquanto ligações. Seguem-se alguns dos erros comuns

#### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>ERRO 1045 (28000): Acesso negado para o utilizador 'username'@'IP address' (usando a palavra-passe: SIM)

O erro acima ocorre se:

* O nome de utilizador não existe
* O nome de utilizador foi eliminado
* a sua palavra-passe é alterada ou reiniciada.

**Resolução:** 
* Valide se o "nome de utilizador" existir como utilizador válido no servidor ou se for acidentalmente eliminado. Pode executar a seguinte consulta iniciando sessão na Base de Dados Azure para o utilizador MySQL:
  ```sql
  select user from mysql.user;
  ```
* Se não conseguir iniciar sessão no MySQL para executar a consulta acima, recomendamos que [reinicie a palavra-passe de administração utilizando o portal Azure](howto-create-manage-server-portal.md). A opção de palavra-passe de reset a partir do portal Azure ajudará a recriar o utilizador, redefinir a palavra-passe e restaurar as permissões de administração, que lhe permitirão iniciar sessão usando o administrador do servidor e realizar novas operações.

## <a name="next-steps"></a>Passos seguintes
Se não encontrou a resposta que procura, considere a seguinte:

- Publique a sua pergunta no [Microsoft Q&Uma página de perguntas](/answers/topics/azure-database-mysql.html) ou Stack [Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Envie um e-mail para a Base de Dados Azure para mySQL Team [ @Ask Azure DB para MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Este endereço de e-mail não é um pseudónimo de suporte técnico.
- Contacte o Suporte Azure, [preencha um bilhete a partir do portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
