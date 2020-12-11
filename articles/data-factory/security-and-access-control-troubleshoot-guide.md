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
ms.openlocfilehash: 99c03ae4430d1a4caf575bdb9900200af0217bf1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109776"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Resolução de problemas Azure Data Factory questões de segurança e controlo de acessos

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para segurança e controlo de acessos na Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns


### <a name="connectivity-issue-of-copy-activity-in-cloud-data-store"></a>Emissão de conectividade da atividade de cópia na Cloud Data Store

#### <a name="symptoms"></a>Sintomas

Vários tipos de mensagens de erro podem ser devolvidas quando ocorreu um problema de conectividade para a loja de dados de origem/pia.

#### <a name="cause"></a>Causa 

O problema é causado principalmente por seguintes fatores:

1. Definição de procuração no nó de IR auto-hospedado (se estiver a utilizar o IR auto-hospedado)

1. Definição de firewall no nó do IR auto-hospedado (se estiver a utilizar o IR auto-hospedado)

1. Definição de firewall na Cloud Data Store

#### <a name="resolution"></a>Resolução

1. Verifique primeiro os seguintes pontos para se certificar de que o problema é causado por problemas de conectividade:

   - O erro é lançado a partir dos conectores de origem/pia.

   - A atividade falha no início da cópia

   - É uma falha consistente para o IR Azure ou ir auto-hospedado com um nó, pois poderia ser uma falha aleatória para o IR auto-hospedado de múltiplos nós se apenas uma parte dos nós tiver o problema.

1. Verifique as definições de procuração, firewall e rede se estiver a utilizar **o IR auto-hospedado,** uma vez que a corrida para a mesma loja de dados pode ter sucesso no Azure IR. Consulte os seguintes links para resolução de problemas:

   [Portos e Firewalls](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls) 
    auto-hospedados [Conector ADLS](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
1. Se estiver a utilizar **o Azure IR,** tente desativar a definição de firewall da loja de dados. Desta forma, a questão para seguir duas circunstâncias pode ser corrigida:
  
   * [Os endereços IP Azure NÃO](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) estão na lista de permitir.

   * *Permitir que serviços fidedignos da Microsoft acedam a esta* funcionalidade de conta de armazenamento é descolorido para [Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) e [ADLS Gen 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities).

   * *Permitir o acesso aos serviços Azure* não está ativado para a ADLS Gen1.

1. Se os métodos acima não estiverem funcionando, contacte a Microsoft para obter ajuda.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Problema chave de autenticação inválida ou vazia após o acesso à rede pública ser desativado

#### <a name="symptoms"></a>Sintomas

Depois de desativar o acesso à rede pública para a Data Factory, porque o tempo de execução de integração auto-alojado lança o seguinte erro: "A chave de autenticação é inválida ou vazia."

#### <a name="cause"></a>Causa

O problema é provavelmente causado por problemas de resolução de DNS, uma vez que a desativação da conectividade pública e o estabelecimento de um ponto final privado não ajudam a reconectar-se.

Pode seguir as etapas abaixo para verificar se a FQDN da Fábrica de Dados está resolvida para o endereço IP público:

1. Confirme que criou o Azure VM no mesmo VNET que o ponto final privado da Data Factory.

2. Executar PsPing e Ping de Azure VM para Data Factory FQDN:

   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`

   > [!Note]
   > É necessária uma porta para o comando PsPing, enquanto a porta 443 não é obrigatória.

3. Verifique se ambos os comandos foram resolvidos a um IP público ADF baseado na região especificada (formato xxx.xxx.xxx.xxx.0).

#### <a name="resolution"></a>Resolução

- Pode consultar o artigo no [Azure Private Link for Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints). A instrução é para configurar a zona/servidor dNS privado para resolver o FQDN da Data Factory para o endereço IP privado.

- Se não estiver disposto a configurar a zona/servidor privado de DNS atualmente, por favor, tome abaixo os passos como solução temporária. No entanto, o DNS personalizado ainda é recomendado como solução a longo prazo.

  1. Altere o ficheiro anfitrião em janelas e mapeeia IP privado (ADF Private endpoint) para ADF FQDN.
  
     Navegue até ao caminho "C:\Windows\System32\drivers\etc" em Azure VM e abra o ficheiro **de anfitrião** com bloco de notas. Adicione a linha de mapeamento de IP privado à FQDN no final do ficheiro e guarde a alteração.
     
     ![Adicione mapeamento ao anfitrião](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Volte a fazer os mesmos comandos em etapas de verificação acima para verificar a resposta, que deve conter o IP privado.

  1. Re-registar o tempo de integração auto-hospedado e o problema deve ser resolvido.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Não é possível registar a chave de autenticação iv em VMs auto-hospedados devido a ligação privada

#### <a name="symptoms"></a>Sintomas

Não é possível registar a chave de autenticação IV em VM auto-hospedado devido a ligação privada ativada.

As informações de erro mostram o seguinte:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Causa

O problema pode ser causado pelo VM em que está a tentar instalar o IR auto-hospedado. Para se ligar à nuvem, deve garantir que o acesso à rede pública está ativado.

#### <a name="resolution"></a>Resolução

 **Solução 1:** Pode seguir os passos abaixo para resolver o problema:

1. Vá às [Fábricas - Página de atualização.](https://docs.microsoft.com/rest/api/datafactory/Factories/Update)

1. No canto superior direito, selecione o botão **Tente-o.**

1. Nos **Parâmetros,** complete as informações necessárias. 

1. Sob **o Corpo,** cole a seguinte propriedade:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Selecione **Executar** para executar a função. 

1. Confirme o **Código de Resposta: 200** é apresentado. A propriedade que colou deve ser exibida na definição JSON também.

1. Adicione novamente a tecla de autenticação IV no tempo de execução da integração.


**Solução 2:** Pode consultar abaixo o artigo para obter uma solução:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Tente permitir o acesso à rede pública na interface do utilizador, como mostra a seguinte imagem:

![Permitir o acesso à rede pública](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda na resolução de problemas, experimente os seguintes recursos:

*  [Link privado para fábrica de dados](data-factory-private-link.md)
*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página do Microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Stack overflow forum para data factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
