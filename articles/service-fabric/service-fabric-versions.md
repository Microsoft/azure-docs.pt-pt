---
title: Versões de cluster suportadas em Tecido de Serviço Azure
description: Conheça as versões de cluster no Azure Service Fabric, incluindo um link para os mais recentes lançamentos do blog da equipa do Service Fabric.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: 82b90d6b0acf5d83f9509abd9ee4b34cb74d1b48
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736789"
---
# <a name="supported-service-fabric-versions"></a>Versões de tecido de serviço suportado

Certifique-se de que o seu cluster está sempre a executar uma versão suportada do Azure Service Fabric. No mínimo 60 dias após anunciarmos o lançamento de uma nova versão do Service Fabric, o suporte para a versão anterior termina. Encontrará anúncios de novos lançamentos no blog da [equipa de Service Fabric.](https://azure.microsoft.com/updates/?product=service-fabric)

Para uma determinada versão do tempo de funcionação do Tecido de Serviço, pode utilizar as versões especificadas ou mais antigas dos pacotes SDK/NuGet. As versões mais recentes dos pacotes não são suportadas e podem ter problemas direcionados para clusters mais antigos, uma vez que podem ter alterações de funcionalidades ou protocolos não suportados por esses ambientes.

Consulte os seguintes documentos para obter mais informações sobre como manter o seu cluster a executar uma versão suportada do Service Fabric:

- [Atualize um cluster de tecido de serviço Azure](service-fabric-cluster-upgrade.md)
- [Atualize a versão 'Service Fabric' que funciona no seu cluster de servidores do Windows autónomo](service-fabric-cluster-upgrade-windows-server.md)


## <a name="unsupported-versions"></a>Versões não suportadas

### <a name="upgrade-alert-for-versions-between-57-and-below-6363"></a>Alerta de atualização para versões entre 5.7 e menos 6.3.63.*
Para melhorar a segurança e a disponibilidade, a infraestrutura Azure fará uma alteração que poderá afetar os clientes do Service Fabric. **Todos os clusters de tecido de serviço em versões não suportadas de 5.7 a 6.3. são impactados**. Resolver a alteração requer uma atualização para o tempo de funcionamento do Service Fabric, que já está disponível para todas as versões suportadas do Service Fabric em todas as regiões.

Solicitamos e recomendamos que tome medidas para atualizar as versões mais recentes suportadas até **19 de janeiro de 2021** para evitar interrupções de serviço Se tiver um plano de suporte e precisar de ajuda técnica, contacte-nos através dos canais de suporte Azure, abrindo um pedido de apoio à Azure Service Fabric e mencione este contexto no bilhete de apoio.

#### <a name="impact-if-not-upgraded-to-supported-versions"></a>Impacto Se NÃO Atualizado para versões suportadas

**Agrupamentos de tecidos de serviço Azure que funcionam em versões não suportadas de 5.7 a 6.3.63. \* não será capaz de aparecer e estará indisponível** se não tiver atualizado para uma das versões suportadas abaixo até 19 de janeiro de 2021.

#### <a name="required-action"></a>Ação Necessária
Atualização para as versões suportadas pelo Tecido de Serviço listadas abaixo para evitar tempo de inatividade ou perda de funcionalidades relacionadas com esta alteração. Certifique-se de que os seus clusters estão a executar pelo menos estas versões para evitar problemas no seu ambiente.

  ###### <a name="supported-service-fabric-runtime-versions"></a>Versões de runtime de tecido de serviço suportado
   Se estiver em NÃO nas versões suportadas abaixo da Service Fabric, faça upgrade para uma destas versões que já contenham as alterações necessárias para evitar o tempo de inatividade do cluster. **Nota:** Todas as versões de lançamento do 7.2 incluem as alterações necessárias.
  
  | SO | Tempo de execução do tecido de serviço atual no cluster | Lançamento CU/Patch  | 
  | --- | --- |--- | 
  | Windows | 7.0.* | 7.0.478.9590 |
  | Windows | 7.1.* | 7.1.503.9590 |
  | Windows | 7.2.* | 7.2.* |
  | Ubuntu 16 | 7.0.* | 7.0.472.1  |
  | Linux Ubuntu 16.04 | 7.1.* | 7.1.455.1  |
  | Linux Ubuntu 18.04 | 7.1.* | 7.1.455.1804 |
  | Linux Ubuntu 16.04 | 7.2.* | 7.2.* |
  | Linux Ubuntu 18.04 | 7.2.* | 7.2.* |
 
### <a name="upgrade-alert-for-versions-greater-than-63"></a>Alerta de upgrade para versões superiores a 6.3 
Para melhorar a segurança e a disponibilidade, a infraestrutura Azure fará uma alteração que poderá afetar os clientes do Service Fabric. **Todos os clusters de tecidos de serviço que utilizam [a função open networking para contentores,](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)funcionam em versões não suportadas superiores a 6.3 e abaixo de 7.0 e são impactadas versões suportadas incompatíveis a partir do ponto 7.0**. Resolver a alteração requer uma atualização para o tempo de funcionamento do Service Fabric, que já está disponível para todas as versões suportadas do Service Fabric em todas as regiões.

 #### <a name="impact-if-not-upgraded-to-supported-versions"></a>Impacto Se NÃO Atualizado para versões suportadas
  Os clusters de tecidos de serviço Azure que **utilizam a funcionalidade open [networking para contentores](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) e funciona em versões superiores a 6.3** que não incluem alterações sentirão perda de funcionalidade ou perturbações de serviço se NÃO for atualizada para uma das versões suportadas abaixo até **19 de janeiro de 2021**.
 
  - **Para os clusters que executam uma versão do Service Fabric superior a 6.3 NÃO utilizando a função Open Networking,** o cluster permanecerá em funcionamento.

 - **Para os clusters que executam uma versão do Service Fabric superior a 6.3 e utilizar [a funcionalidade open networking para contentores](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)** , o cluster pode ficar indisponível e deixará de funcionar, o que poderá causar interrupções de serviço nas suas cargas de trabalho.
 
 -   **Para os clusters que executam as [versões do Windows entre 7.0 e 7.0.466 (ambas as versões incluídas)](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-version-names) e o Windows OS tem a Funcionalidade de Contentores do Windows ativada. NOTA: As versões Linux 7.0.457, 7.0.464 e 7.0.465 NÃO são impactadas**.
    - **Impacto**: O cluster deixará de funcionar, o que poderá provocar interrupções de serviço nas suas cargas de trabalho.
       
#### <a name="required-action"></a>Ação Necessária
Atualização para as versões suportadas pelo Tecido de Serviço listadas abaixo para evitar tempo de inatividade ou perda de funcionalidades relacionadas com esta alteração. Certifique-se de que os seus clusters estão a executar pelo menos estas versões para evitar problemas no seu ambiente. 
 
 ###### <a name="supported-service-fabric-runtime-versions"></a>Versões de runtime de tecido de serviço suportado
 Se estiver em NÃO nas versões suportadas abaixo da Service Fabric, faça upgrade para uma destas versões que já contêm as alterações necessárias para evitar a perda de funcionalidade.  **Nota:** Todas as versões de lançamento do 7.2 incluem as alterações necessárias.
 
  | SO | Tempo de execução do tecido de serviço atual no cluster | Lançamento CU/Patch  | 
  | --- | --- |--- | 
  | Windows | 7.0.* | 7.0.478.9590 |
  | Windows | 7.1.* | 7.1.503.9590 |
  | Windows | 7.2.* | 7.2.* |
  | Linux Ubuntu 16.04 | 7.0.* | 7.0.472.1  |
  | Linux Ubuntu 16.04 | 7.1.* | 7.1.455.1  |
  | Linux Ubuntu 18.04 | 7.1.* | 7.1.455.1804 |
  | Linux Ubuntu 16.04 | 7.2.* | 7.2.* |
  | Linux Ubuntu 18.04 | 7.2.* | 7.2.* |

## <a name="supported-versions"></a>Versões suportadas
A tabela que se segue lista as versões do Service Fabric e as datas finais de suporte.

| Tempo de execução do tecido de serviço no cluster | Pode atualizar diretamente a partir da versão cluster |Versão compatível do pacote SDK ou NuGet | Fim do apoio |
| --- | --- |--- | --- |
| Todas as versões do cluster antes de 5.3.121 | 5.1.158.* |Menos ou igual à versão 2.3 |20 de janeiro de 2017 |
| 5.3.* | 5.1.158.* |Menos ou igual à versão 2.3 |24 de fevereiro de 2017 |
| 5.4.* | 5.1.158.* |Menos ou igual à versão 2.4 |10 de maio de 2017       |
| 5.5.* | 5.4.164.* |Menos ou igual à versão 2.5 |10 de agosto de 2017    |
| 5.6.* | 5.4.164.* |Menos ou igual à versão 2.6 |13 de outubro de 2017   |
| 5.7.* | 5.4.164.* |Menos ou igual à versão 2.7 |15 de dezembro de 2017  |
| 6.0.* | 5.6.205.* |Menos ou igual à versão 2.8 |30 de março de 2018     |
| 6.1.* | 5.7.221.* |Menos ou igual à versão 3.0 |15 de julho de 2018      |
| 6.2.* | 6.0.232.* |Menos ou igual à versão 3.1 |26 de outubro de 2018   |
| 6.3.* | 6.1.480.* |Menos ou igual à versão 3.2 |31 de março de 2019  |
| 6.4.* | 6.2.301.* |Menos ou igual à versão 3.3 |15 de setembro de 2019 |
| 6.5.* | 6.4.617.* |Menos ou igual à versão 3.4 |1 de agosto de 2020 |
| 7.0.466.* | 6.4.664.* |Menos ou igual à versão 4.0|31 de janeiro de 2021  |
| 7.0.466.* | 6.5.* |Menos ou igual à versão 4.0|31 de janeiro de 2021 |
| 7.0.470.* | 7.0.466.* |Menos ou igual à versão 4.0 |31 de janeiro de 2021  |
| 7.0.472.* | 7.0.466.* |Menos ou igual à versão 4.0 |31 de janeiro de 2021  |
| 7.0.478.* | 7.0.466.* |Menos ou igual à versão 4.0 |31 de janeiro de 2021  |
| 7.1.409.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.1.417.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.1.428.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.1.456.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.1.458.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.1.459.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.1.503.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.2.413.* | 7.0.470.* |Menos ou igual à versão 4.2 |Versão atual, então sem data de fim |
| 7.2.432.* | 7.0.470.* |Menos ou igual à versão 4.2 |Versão atual, então sem data de fim |
| 7.2.433.* | 7.0.470.* |Menos ou igual à versão 4.2 |Versão atual, então sem data de fim |
| 7.2.445.* | 7.0.470.* |Menos ou igual à versão 4.2 |Versão atual, então sem data de fim |

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

A tabela que se segue lista os sistemas operativos suportados para as versões suportadas do Service Fabric.

| Sistema operativo | Primeira versão suportada do Tecido de Serviço |
| --- | --- |
| Windows Server 2012 R2 | Todas as versões |
| Windows Server 2016 | Todas as versões |
| Servidor Windows 1709 | 6,0 |
| Servidor Windows 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6,0 |
| Linux Ubuntu 18.04 | 7.1 |

## <a name="supported-version-names"></a>Nomes de versão suportados

A tabela que se segue lista os nomes da versão do Service Fabric e os respetivos números de versão.

| Nome da versão | Número da versão do Windows | Número da versão Linux |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | ND |
| 5.3 CU1 | 5.3.204.9494 | ND |
| 5.3 CU2 | 5.3.301.9590 | ND |
| 5.3 CU3 | 5.3.311.9590 | ND |
| 5.4 CU2 | 5.4.164.9494 | ND |
| 5.5 CU1 | 5.5.216.0    | ND |
| 5.5 CU2 | 5.5.219.0    | ND |
| 5.5 CU3 | 5.5.227.0    | ND |
| 5.5 CU4 | 5.5.232.0 | ND |
| 5.6 RTO | 5.6.204.9494 | ND |
| 5.6 CU2 | 5.6.210.9494 | ND |
| 5.6 CU3 | 5.6.220.9494 | ND |
| 5.7 RTO | 5.7.198.9494 | ND |
| 5.7 CU4 | 5.7.221.9494 | ND |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | ND |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | ND |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | ND |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU8 | 6.4.670.9590 | ND |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7.0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7.0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7.1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7.1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7.1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7.1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7.1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7.1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7.1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7.2 RTO | 7.2.413.9590 | ND |
| 7.2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7.2 CU3 | 7.2.433.9590 | ND |
| 7.2 CU4 | 7.2.445.9590 | 7.2.447.1 |

