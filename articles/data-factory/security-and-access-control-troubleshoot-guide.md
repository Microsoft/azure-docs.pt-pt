---
title: Problemas de segurança e controlo de acessos
description: Saiba como resolver problemas de segurança e controlo de acessos na Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008735"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Resolução de problemas Azure Data Factory questões de segurança e controlo de acessos

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para segurança e controlo de acessos na Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>Problema chave de autenticação inválido ou vazio após desativar o acesso à rede pública

#### <a name="symptoms"></a>Sintomas

O tempo de execução de integração auto-alojado lança o erro "A chave de autenticação é inválida ou vazia" depois de desativar o acesso público à rede de dados.

#### <a name="cause"></a>Causa

O problema é provavelmente causado por problemas de resolução de DNS, uma vez que a desativação da conectividade pública e o estabelecimento de um ponto final privado não ajudam a reconectar-se.

#### <a name="resolution"></a>Resolução

1. Fez um PsPing para o FQDN da ADF e descobriu que o tampão ia para um ponto final público da ADF, mesmo depois de o ter desativado.

1. Altere o ficheiro anfitrião em VM para mapear IP privado para FQDN e executar novamente um PsPing. O tampão poderá ir ao IP privado correto da ADF.

1. Re-registrar o tempo de integração auto-hospedado e vamos encontrá-lo em funcionamento.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Não é possível registar a chave de autenticação iv em VMs auto-hospedados devido a ligação privada

#### <a name="symptoms"></a>Sintomas

Não é possível registar a chave de autenticação IV em VM auto-hospedado devido a ligação privada ativada.

As informações de erro mostram o seguinte:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Causa

A questão pode ser causada pelo VM onde o SHIR está a tentar ser instalado. O acesso à rede pública deve ser ativado para a ligação à nuvem.

#### <a name="resolution"></a>Resolução

 **Solução 1:** Pode seguir os passos abaixo para resolver o problema:

1. Navegue para o link abaixo: 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Clique na opção **Try It** e preencha todos os detalhes necessários. Cole abaixo da propriedade no **Corpo** também:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Clique em **Executar** no final da página para executar a função. Certifique-se de que receberá o Código de Resposta 200. A propriedade que colámos também será mostrada na definição JSON.

1. Em seguida, pode tentar adicionar a chave de autenticação IR novamente no tempo de integração.


**Solução 2:** Pode consultar abaixo o artigo para obter uma solução:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Tente ativar o acesso à rede pública com interface de utilizador.

![Permitir o acesso à rede pública](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda na resolução de problemas, experimente os seguintes recursos:

*  [Link privado para fábrica de dados](data-factory-private-link.md)
*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](/answers/topics/azure-data-factory.html)
*  [Stack overflow forum para data factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)