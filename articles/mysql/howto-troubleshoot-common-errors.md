---
title: Resolução de erros comuns - Base de Dados Azure para MySQL
description: Saiba como resolver problemas de erros de migração encontrados pelos utilizadores novos na Base de Dados Azure para o serviço MySQL
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: 8f735ecd4f8b79b4f5bd0c95d0bfb9f280d93833
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387348"
---
# <a name="common-errors"></a>Erros comuns

Azure Database for MySQL é um serviço totalmente gerido alimentado pela versão comunitária do MySQL. A experiência MySQL num ambiente de serviço gerido pode diferir de executar o MySQL no seu próprio ambiente. Neste artigo, irá ver alguns dos erros comuns que os utilizadores podem encontrar durante a migração ou desenvolvimento na Base de Dados Azure para o serviço MySQL pela primeira vez.

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

**Resolução**: Para resolver o erro, desacrie log_bin_trust_function_creators a 1 a partir da lâmina dos [parâmetros](howto-server-parameters.md) do servidor no portal, execute as declarações DDL ou importe o esquema para criar os objetos pretendidos e reverta o parâmetro log_bin_trust_function_creators para o seu valor anterior após a criação.

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

## <a name="next-steps"></a>Passos Seguintes
Se não encontrou a resposta que procurava, considere seguir:
- Publique a sua pergunta no [Microsoft Q&Uma página de perguntas](/answers/topics/azure-database-mysql.html) ou Stack [Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Envie um e-mail para a Base de Dados Azure para mySQL Team [ @Ask Azure DB para MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Este endereço de e-mail não é um pseudónimo de suporte técnico.
- Contacte o Suporte Azure, [preencha um bilhete a partir do portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
