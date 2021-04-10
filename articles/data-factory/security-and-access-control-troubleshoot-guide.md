---
title: Problemas de segurança e controlo de acessos
description: Saiba como resolver problemas de segurança e controlo de acessos na Azure Data Factory.
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.author: lle
ms.openlocfilehash: 5e94ea989002d3d3c6d0e96123d5b8ddb5f078c3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568040"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Resolução de problemas Azure Data Factory questões de segurança e controlo de acessos

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para segurança e controlo de acessos na Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>Problema de conectividade na atividade de cópia da loja de dados em nuvem

#### <a name="symptoms"></a>Sintomas

Várias mensagens de erro podem ser devolvidas quando ocorrem problemas de conectividade na loja de dados de origem ou afunda.

#### <a name="cause"></a>Causa 

O problema é geralmente causado por um dos seguintes fatores:

* A definição de procuração no nó de execução de integração auto-hospedada (IR), se estiver a utilizar um IR auto-hospedado.

* A definição de firewall no nó ir auto-hospedado, se estiver a usar um IR auto-hospedado.

* A definição de firewall na loja de dados em nuvem.

#### <a name="resolution"></a>Resolução

* Para garantir que se trata de um problema de conectividade, verifique os seguintes pontos:

   - O erro é atirado da fonte ou dos conectores da pia.
   - A falha está no início da atividade de cópia.
   - A falha é consistente para o Azure IR ou para o IR auto-hospedado com um nó, porque pode ser uma falha aleatória num IR auto-hospedado de múltiplos nós se apenas alguns dos nós tiverem o problema.

* Se estiver a utilizar um **IR auto-hospedado,** verifique as definições de procuração, firewall e rede, porque a ligação à mesma datastore pode ter sucesso se estiver a utilizar um Azure IR. Para resolver este cenário, consulte:

   * [Portas e firewalls auto-hospedados](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Conector de armazenamento do lago de dados Azure](./connector-azure-data-lake-store.md)
  
* Se estiver a utilizar um **Azure IR,** tente desativar a definição de firewall da datastore. Esta abordagem pode resolver as questões nas duas situações seguintes:
  
   * [Os endereços IP Azure](./azure-integration-runtime-ip-addresses.md) NÃO estão na lista de autorizações.
   * O *Allow trust microsoft services to access this storage account* feature is des desligado para [Azure Blob Storage](./connector-azure-blob-storage.md#supported-capabilities) e [Azure Data Lake Storage Gen 2](./connector-azure-data-lake-storage.md#supported-capabilities).
   * O *acesso ao acesso a Azure não* está habilitado para a Azure Data Lake Storage Gen1.

Se nenhum dos métodos anteriores funcionar, contacte a Microsoft para obter ajuda.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Problema chave de autenticação inválida ou vazia após o acesso à rede pública ser desativado

#### <a name="symptoms"></a>Sintomas

Depois de desativar o acesso à rede pública para a Data Factory, o tempo de execução de integração auto-alojado lança o seguinte erro: "A tecla de autenticação é inválida ou vazia."

#### <a name="cause"></a>Causa

O problema é provavelmente causado por um problema de resolução do Sistema de Nome de Domínio (DNS), porque desativar a conectividade pública e estabelecer um ponto final privado impede a reconexão.

Para verificar se o nome de domínio totalmente qualificado (FQDN) da Data Factory é resolvido para o endereço IP público, faça o seguinte:

1. Confirme que criou a máquina virtual Azure (VM) na mesma rede virtual que o ponto final privado da Data Factory.

2. Executar PsPing e Ping do Azure VM para a Data Factory FQDN:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > Tem de especificar uma porta para o comando PsPing. A porta 443 é mostrada aqui, mas não é necessária.

3. Verifique se ambos os comandos se resolvem para um IP público da Azure Data Factory que se baseia numa região especificada. O IP deve estar no seguinte formato: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Resolução

Para resolver a questão, faça o seguinte:

- Como opção, gostaríamos de sugerir que adicione manualmente uma "ligação de Rede Virtual" na Fábrica de Dados "Zona DS de ligação privada". Para mais informações, consulte o Azure Private Link para o artigo [da Azure Data Factory.](./data-factory-private-link.md#dns-changes-for-private-endpoints) A instrução é para configurar a zona privada de DNS ou o servidor DNS personalizado para resolver o FQDN da Data Factory para um endereço IP privado. 

- No entanto, se não quiser configurar a zona privada de DNS ou o servidor DNS personalizado, experimente a seguinte solução temporária:

  1. Altere o ficheiro anfitrião no Windows e mapeeeia o IP privado (o ponto final privado da Azure Data Factory) para o Azure Data Factory FQDN.
  
     No Azure VM, vá a `C:\Windows\System32\drivers\etc` , e depois abra o ficheiro de *anfitrião* no Bloco de Notas. Adicione a linha que mapeia o IP privado ao FQDN no final do ficheiro e guarde a alteração.
     
     ![Screenshot de mapear o IP privado para o anfitrião.](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Volte a fazer os mesmos comandos que nas etapas de verificação anteriores para verificar a resposta, que deve conter o IP privado.

  1. Re-registar o tempo de integração auto-hospedado, e a questão deve ser resolvida.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Não é possível registar a chave de autenticação iv em VMs auto-hospedados devido a ligação privada

#### <a name="symptoms"></a>Sintomas

Não é possível registar a chave de autenticação IV no VM auto-hospedado porque o link privado está ativado. Recebe a seguinte mensagem de erro:

"Falhando na obter o token de serviço do serviço ADF com a chave *** e o custo de tempo é: 0.1250079 segundos, o código de erro é: InvalidGatewayKey, activityId é: XXXXXX E a mensagem de erro detalhada é o endereço IP do cliente não é válido por ip fábrica de dados não poderia aceder à rede pública, assim não conseguir chegar à nuvem para fazer a ligação com sucesso."

#### <a name="cause"></a>Causa

O problema pode ser causado pelo VM em que está a tentar instalar o IR auto-hospedado. Para se ligar à nuvem, certifique-se de que o acesso à rede pública está ativado.

#### <a name="resolution"></a>Resolução

**Solução 1**
 
Para resolver a questão, faça o seguinte:

1. Vá às [Fábricas - Página de atualização.](/rest/api/datafactory/Factories/Update)

1. No canto superior direito, selecione o botão **Tente-o.**
1. Nos **Parâmetros,** complete as informações necessárias. 
1. Sob **o Corpo,** cole a seguinte propriedade:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. Selecione **Executar** para executar a função. 

1. Nos **Parâmetros,** complete as informações necessárias. 

1. Sob **o Corpo,** cole a seguinte propriedade:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Selecione **Executar** para executar a função. 
1. Confirme o **Código de Resposta: 200** é apresentado. A propriedade que colou deve ser exibida na definição JSON também.

1. Adicione novamente a tecla de autenticação IV no tempo de execução da integração.

**Solução 2**

Para resolver o problema, vá ao [Azure Private Link for Azure Data Factory](./data-factory-private-link.md).

Tente permitir o acesso à rede pública na interface do utilizador, como mostra a seguinte imagem:

![Screenshot do controlo "Ativado" para "Permitir o acesso à rede pública" no painel de networking.](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

### <a name="adf-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>Zona privada de DNS da ADF substitui resolução de DNS do Gestor de Recursos Azure causando erro "Não encontrado"

#### <a name="cause"></a>Causa
Tanto o Azure Resource Manager como a ADF estão a usar a mesma zona privada, criando um potencial conflito no DNS privado do cliente, com um cenário em que os registos do Azure Resource Manager não serão encontrados.

#### <a name="solution"></a>Solução
1. Encontre zonas privadas de DNS **privatelink.azure.com** no portal Azure.
![Screenshot de encontrar zonas privadas de DNS.](media/security-access-control-troubleshoot-guide/private-dns-zones.png)
2. Verifique se existe **um adf de** registo A .
![Screenshot de um disco.](media/security-access-control-troubleshoot-guide/a-record.png)
3.  Aceda a **links de rede Virtuais,** elimine todos os registos.
![Screenshot da ligação de rede virtual.](media/security-access-control-troubleshoot-guide/virtual-network-link.png)
4.  Navegue até à sua fábrica de dados no portal Azure e recrie o ponto final privado para o portal Azure Data Factory.
![Screenshot de recriar o ponto final privado.](media/security-access-control-troubleshoot-guide/create-private-endpoint.png)
5.  Volte para as zonas privadas de DNS e verifique se existe uma nova zona privada de DNS **privatelink.adf.azure.com**.
![Screenshot do novo disco DNS.](media/security-access-control-troubleshoot-guide/check-dns-record.png)

### <a name="connection-error-in-public-endpoint"></a>Erro de ligação no ponto final público

#### <a name="symptoms"></a>Sintomas

Ao copiar dados com o acesso público da conta Azure Blob, o gasoduto falha aleatoriamente com o seguinte erro.

Por exemplo: A pia Azure Blob Storage estava a utilizar o Azure IR (público, não gerido VNet) e a fonte da Base de Dados Azure SQL estava a utilizar o ViaTDD IR. Ou utilização de fonte/pia Managed VNet IR apenas com acesso público de armazenamento.

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>Causa

A ADF pode ainda utilizar o Managed VNet IR, mas pode encontrar tal erro porque o ponto final público do Azure Blob Storage em Managed VNet não é fiável com base no resultado dos testes, e o Azure Blob Storage e a Azure Data Lake Gen2 não são suportados para serem conectados através de um ponto final público da ADF Managed Virtual Network de acordo com a [rede virtual gerida & pontos finais privados geridos](./managed-virtual-network-private-endpoint.md#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network).

#### <a name="solution"></a>Solução

- Ter o ponto final privado ativado na fonte e também o lado da pia ao utilizar o VNet IR gerido.
- Se ainda quiser utilizar o ponto final público, só pode mudar para o IR público em vez de utilizar o Ir Gerido VNet para a fonte e o lavatório. Mesmo que volte a ir publicamente, a ADF poderá ainda utilizar o Ir Gerido VNet se o VNet IR gerido ainda estiver lá.

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda na resolução de problemas, experimente os seguintes recursos:

*  [Link privado para fábrica de dados](data-factory-private-link.md)
*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página do Microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Stack overflow forum para data factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)