---
title: Problemas de autenticação no Azure HDInsight
description: Problemas de autenticação no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.openlocfilehash: 9f10597023314aed8640ee5a7499a77f952c3694
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284439"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Problemas de autenticação no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

Em aglomerados seguros apoiados por Azure Data Lake (Gen1 ou Gen2), quando os utilizadores de domínios se inscrevem nos serviços de cluster através do HDI Gateway (como iniciar sessão no portal Apache Ambari), o HDI Gateway tentará obter um token OAuth da Azure Ative Directory (Azure AD) primeiro, e depois obter um bilhete Kerberos a partir de Azure AD DS. A autenticação pode falhar em qualquer uma destas fases. Este artigo destina-se a depurar algumas dessas questões.

Quando a autenticação falhar, será solicitado para obter credenciais. Se cancelar este diálogo, a mensagem de erro será impressa. Aqui estão algumas das mensagens de erro comuns:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant ou unauthorized_client, 50126

### <a name="issue"></a>Problema

A sucção de sucção é de falhas para utilizadores federados com o código de erro 50126 (a sinagem tem sucesso para os utilizadores da nuvem). A mensagem de erro é semelhante a:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Causa

O código de erro 50126 da AD Azure significa que a `AllowCloudPasswordValidation` apólice não foi definida pelo arrendatário.

### <a name="resolution"></a>Resolução

O Administrador da Empresa do inquilino Azure AD deve permitir que a Azure AD utilize hashes de palavra-passe para utilizadores apoiados pela ADFS.  Aplique o `AllowCloudPasswordValidationPolicy` como mostrado no artigo Use Pacote de Segurança [Empresarial em HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant ou unauthorized_client, 50034

### <a name="issue"></a>Problema

O sinal de s.a.S.A. falha com o código de erro 50034. A mensagem de erro é semelhante a:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Causa

O nome de utilizador está incorreto (não existe). O utilizador não está a utilizar o mesmo nome de utilizador que é utilizado no portal Azure.

### <a name="resolution"></a>Resolução

Use o mesmo nome de utilizador que funciona nesse portal.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant ou unauthorized_client, 50053

### <a name="issue"></a>Problema

A conta de utilizador está bloqueada, código de erro 50053. A mensagem de erro é semelhante a:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Causa

Demasiados sinal de sinal em tentativas com uma senha incorreta.

### <a name="resolution"></a>Resolução

Aguarde mais ou menos 30 minutos, pare quaisquer aplicações que possam estar a tentar autenticar.

---

## <a name="invalid_grant-or-unauthorized_client-50053-2"></a>invalid_grant ou unauthorized_client, 50053 (#2)

### <a name="issue"></a>Problema

A palavra-passe expirou, código de erro 50053. A mensagem de erro é semelhante a:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Causa

A senha expirou.

### <a name="resolution"></a>Resolução

Altere a palavra-passe no portal Azure (no seu sistema no local) e aguarde 30 minutos para que a sincronização se apanhe.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problema

Receber mensagem de erro `interaction_required` .

### <a name="cause"></a>Causa

A política de acesso condicional ou a MFA está a ser aplicada ao utilizador. Como a autenticação interativa ainda não é suportada, a política de Acesso condicional/MFA não deverá estar ativada para o utilizador ou cluster. Se optar por isentar o cluster (política de isenção baseada em endereços IP), certifique-se de que a AD `ServiceEndpoints` está ativada para esse vnet.

### <a name="resolution"></a>Resolução

Utilize a política de acesso condicional e isenta os clusters HDInisght da MFA, conforme mostrado no [Configure um cluster HDInsight com o Pacote de Segurança Empresarial utilizando os Serviços de Domínio do Diretório Ativo Azure](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Assinar em negado

### <a name="issue"></a>Problema

O sinal é negado.

### <a name="cause"></a>Causa

Para chegar a esta fase, a sua autenticação OAuth não é um problema, mas a autenticação de Kerberos é. Se este cluster for apoiado pela ADLS, o sinal de OAuth foi bem sucedido antes da tentativa de Kerberos auth. Nos aglomerados WASB, o sinal de OAuth não é tentado. Pode haver muitas razões para a falha de Kerberos - como as hashes de palavra-passe estão fora de sincronização, a conta de utilizador bloqueada em Azure AD DS, e assim por diante. A palavra-passe só sincroniza quando o utilizador muda a palavra-passe. Quando criar a instância AD DS do Azure, começará a sincronizar senhas que são alteradas após a criação. Não sincronizará retroativamente senhas que foram definidas antes da sua criação.

### <a name="resolution"></a>Resolução

Se acha que as palavras-passe podem não estar sincronizadas, tente alterar a palavra-passe e aguarde alguns minutos para sincronizar.

Tente sSH em um Você precisará tentar autenticar (kinit) usando as mesmas credenciais de utilizador, a partir de uma máquina que está unida ao domínio. SSH no nó cabeça/borda com um utilizador local e, em seguida, executar kinit.

---

## <a name="kinit-fails"></a>kinit falha

### <a name="issue"></a>Problema

Kinit falha.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

Para que o kinit tenha sucesso, precisa de conhecer o seu `sAMAccountName` (este é o nome da conta curta sem o reino). `sAMAccountName` é geralmente o prefixo da conta (como bob in `bob@contoso.com` ). Para alguns utilizadores, pode ser diferente. Você precisará da capacidade de navegar / pesquisar o diretório para aprender o seu `sAMAccountName` .

Formas de `sAMAccountName` encontrar:

* Se você pode iniciar súpido em Ambari usando o administrador local Ambari, veja a lista de utilizadores.

* Se tiver uma [máquina de janelas de domínio,](../../active-directory-domain-services/tutorial-create-management-vm.md)pode utilizar as ferramentas padrão do Windows AD para navegar. Isto requer uma conta de trabalho no domínio.

* A partir do nó de cabeça, pode usar comandos SAMBA para procurar. Isto requer uma sessão de Kerberos válida (kinit de sucesso). pesquisa de anúncios líquidos "(userPrincipalName=bob*)"

    Os resultados de pesquisa/navegação devem mostrar-lhe o `sAMAccountName` atributo. Além disso, pode olhar para outros atributos como `pwdLastSet` `badPasswordTime` , `userPrincipalName` etc. para ver se essas propriedades correspondem ao que você espera.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit falha com falha pré-autorização

### <a name="issue"></a>Problema

Kinit falha com `Preauthentication` o fracasso.

### <a name="cause"></a>Causa

Nome de utilizador ou senha incorreto.

### <a name="resolution"></a>Resolução

Verifique o seu nome de utilizador e senha. Verifique também outras propriedades acima descritas. Para permitir a depuragem verbose, corra `export KRB5_TRACE=/tmp/krb.log` da sessão antes de experimentar kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>O comando Job / HDFS falha devido à TokenNotFoundException

### <a name="issue"></a>Problema

O comando Job / HDFS falha devido a `TokenNotFoundException` .

### <a name="cause"></a>Causa

O token de acesso OAuth necessário não foi encontrado para que o trabalho/comando tivesse sucesso. O controlador ADLS / ABFS tentará recuperar o token de acesso OAuth do serviço de credencial antes de fazer pedidos de armazenamento. Este token é registado quando faz sê-lo no portal Ambari utilizando o mesmo utilizador.

### <a name="resolution"></a>Resolução

Certifique-se de que iniciou sessão no portal Ambari uma vez através do nome de utilizador cuja identidade é usada para executar o trabalho.

---

## <a name="error-fetching-access-token"></a>Erro de busca de acesso ao token

### <a name="issue"></a>Problema

O utilizador recebe uma mensagem de `Error fetching access token` erro.

### <a name="cause"></a>Causa

Este erro ocorre intermitentemente quando os utilizadores tentam aceder à ADLS Gen2 usando ACLs e o token Kerberos expirou.

### <a name="resolution"></a>Resolução

* Para a Azure Data Lake Storage Gen1, limpe a cache do navegador e volte a entrar em Ambari.

* Para a Azure Data Lake Storage Gen2, corra `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>` para o utilizador que o utilizador está a tentar iniciar sessão como

---

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]