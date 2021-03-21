---
title: Implementar Horizonte na Solução VMware Azure
description: Saiba como implementar o VMware Horizon na Solução VMware Azure.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: c34d0ac7806f8284e893cf3ad4f3c82dd404ff41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181402"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Implementar Horizonte na Solução VMware Azure 

>[!NOTE]
>Este documento centra-se no produto VMware Horizon, anteriormente conhecido como Horizonte 7. Horizonte é uma solução diferente da Horizon Cloud no Azure, embora existam alguns componentes partilhados. As principais vantagens da Solução Azure VMware incluem tanto um método de dimensionamento mais simples como a integração da gestão da VMware Cloud Foundation no portal Azure.

[A VMware Horizon](https://www.vmware.com/products/horizon.html)®, uma plataforma virtual de desktop e aplicações, funciona no centro de dados e fornece uma gestão simples e centralizada. Fornece desktops virtuais e aplicações em qualquer dispositivo, em qualquer lugar. O Horizonte permite criar e intermediar ligações a desktops virtuais Windows e Linux, aplicações, desktops e máquinas físicas do Remote Desktop Server (RDS).

Aqui, focamo-nos especificamente na implementação da Horizon na Azure VMware Solution. Para obter informações gerais sobre a VMware Horizon, consulte a documentação de produção da Horizon:

* [O que é VMware Horizon?](https://www.vmware.com/products/horizon.html)

* [Saiba mais sobre a VMware Horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Arquitetura de Referência Horizonte](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Com a introdução da Horizon na Azure VMware Solution, existem agora duas soluções virtual de Infraestruturas de Ambiente de Trabalho (VDI) na plataforma Azure. O diagrama seguinte resume as diferenças-chave a um nível elevado.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Horizonte em Azure VMware Solution e Nuvem Horizon em Azure" border="false":::

As versões Horizonte 2006 e posteriores na linha de lançamento do Horizonte 8 suportam a implementação no local e a implementação da Solução VMware Azure. Existem algumas funcionalidades da Horizon que são suportadas no local, mas não na Azure VMware Solution. São também apoiados produtos adicionais no ecossistema Horizon. Para obter informações, consulte [paridade de características e interoperabilidade.](https://kb.vmware.com/s/article/80850)

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Implementar Horizonte numa nuvem híbrida

Pode implementar a Horizon num ambiente de nuvem híbrida quando utilizar o Horizon Cloud Pod Architecture (CPA) para interligar no local e nos datacenters Azure. A CPA aumenta a sua implantação, constrói uma nuvem híbrida e proporciona redundância para a Continuidade do Negócio e Recuperação de Desastres.  Para mais informações, consulte [a Expansão do Horizonte 7 Ambientes.](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874)

>[!IMPORTANT]
>A CPA não é uma implantação estendida; cada cápsula Horizon é distinta, e todos os Servidores de Conexão que pertencem a cada uma das cápsulas individuais são obrigados a ser localizados num único local e executados no mesmo domínio de transmissão a partir de uma perspetiva de rede.

Tal como no local ou num centro de dados privado, a Horizon pode ser implantada numa nuvem privada Azure VMware Solution. Discutiremos as principais diferenças na implementação da Horizon no local e na Azure VMware Solution nas seguintes secções.

A nuvem privada Azure é conceptualmente a mesma que o VMware SDDC, um termo tipicamente usado na documentação horizonte. O resto deste documento utiliza os termos Azure private cloud e VMware SDDC intercambiáveis.

O Conector De Nuvem Horizon é necessário para a Horizon on Azure VMware Solution para gerir licenças de subscrição. O Conector cloud pode ser implantado na Rede Virtual Azure ao lado dos Servidores de Conexão Horizonte.

>[!IMPORTANT]
>Horizonte Control Plane suporte para Horizonte na Azure VMware Solution ainda não está disponível. Certifique-se de baixar a versão VHD do Horizon Cloud Connector.

## <a name="vcenter-cloud-admin-role"></a>vCenter Cloud Admin

Uma vez que a Azure VMware Solution é um serviço SDDC e a Azure gere o ciclo de vida do SDDC na Azure VMware Solution, o modelo de permissão vCenter na Azure VMware Solution é limitado por design.

Os clientes são obrigados a usar a função Cloud Admin, que tem um conjunto limitado de permissões vCenter. O produto Horizon foi modificado para trabalhar com o papel de Cloud Admin na Azure VMware Solution, especificamente:

* O fornecimento instantâneo de clones foi modificado para funcionar na Azure VMware Solution.

* Foi criada uma política específica de vSAN (VMware_Horizon) na Azure VMware Solution para trabalhar com a Horizon, que deve estar disponível e utilizada nos SDDCs implantados para horizonte.

* vSphere Content-Based Read Cache (CBRC), também conhecido como Vise Storage Accelerator, é desativado quando funciona na Solução VMware Azure.

>[!IMPORTANT]
>O CBRC não deve voltar a ser ligado.

>[!NOTE]
>A Azure VMware Solution configura automaticamente definições horizoniais específicas desde que implemente o Horizonte 2006 (também conhecido como Horizonte 8) e acima na sucursal do Horizonte 8 e selecione a opção **Azure** no instalador do Servidor de Conexão Horizonte.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Horizonte na arquitetura de implementação de solução VMware Azure

Um design de arquitetura horizonte típico usa uma estratégia de pod e bloco. Um bloco é um único vCenter, enquanto vários blocos combinados fazem uma vagem. Um pod Horizon é uma unidade de organização determinada pelos limites de escalabilidade horizonte. Cada cápsula Horizon tem um portal de gestão separado, e por isso uma prática de design padrão é para minimizar o número de cápsulas.

Cada nuvem tem o seu próprio esquema de conectividade de rede. Combinada com a rede VMware SDDC / NSX Edge, a conectividade da rede Azure VMware Solution apresenta requisitos únicos para a implementação do Horizon que é diferente do local.

Cada nuvem privada Azure e SDDC podem lidar com 4.000 sessões de desktop ou aplicações, assumindo:

* O tráfego de carga de trabalho alinha-se com o perfil do trabalhador de tarefas LoginVSI.

* Apenas o tráfego de protocolo é considerado, nenhum dado do utilizador.

* O NSX Edge está configurado para ser grande.

>[!NOTE]
>O seu perfil de carga de trabalho e as suas necessidades podem ser diferentes, pelo que os resultados podem variar em função do seu caso de utilização. Os volumes de Dados do Utilizador podem reduzir os limites de escala no contexto da sua carga de trabalho. Tamanho e planeie a sua implantação em conformidade. Para obter mais informações, consulte as diretrizes de dimensionamento nos [anfitriões da Solução VMware Size Azure para implementações horizon.](#size-azure-vmware-solution-hosts-for-horizon-deployments)

Dada a nuvem privada Azure e o limite máximo do SDDC, recomendamos uma arquitetura de implementação onde os Servidores de Conexão Horizonte e os Gateways de Acesso Unificados de VMware (UAGs) estão a funcionar dentro da Rede Virtual Azure. Transforma efetivamente cada nuvem privada Azure e SDDC num bloco. Por sua vez, maximizando a escalabilidade da Horizon em execução na Azure VMware Solution.

A ligação da Rede Virtual Azure às nuvens privadas Azure /SDDCs deve ser configurada com ExpressRoute FastPath. O diagrama seguinte mostra uma implantação básica da cápsula Horizon.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Implantação típica do pod Horizonte usando o Caminho Rápido ExpressPath" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Conectividade de rede para escalar Horizonte na Solução VMware Azure

Esta secção define a arquitetura da rede a um nível elevado com alguns exemplos de implementação comuns para ajudá-lo a escalar horizonte em Azure VMware Solution. O foco está especificamente em elementos críticos de networking. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Cápsula Single Horizon na Solução VMware Azure

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Cápsula Single Horizon na Solução VMware Azure" border="false":::

Uma única cápsula Horizon é o cenário de implantação mais simples porque implementa apenas uma cápsula Horizon na região leste dos EUA.  Uma vez que cada nuvem privada e SDDC são estimados para lidar com 4.000 sessões de desktop, você implementa o tamanho máximo do pod horizon.  Você pode planear a implantação de até três nuvens/SDDCs privados.

Com as máquinas virtuais de infraestrutura Horizon (VMs) implantadas na Rede Virtual Azure, pode chegar às 12.000 sessões por cápsula Horizon. A ligação entre cada nuvem privada e SDDC à Rede Virtual Azure é o Caminho Rápido ExpressRoute.  Não é necessário tráfego leste-oeste entre nuvens privadas. 

Os pressupostos-chave para este exemplo de implantação básica incluem que:

* Você não tem um pod Horizon no local que você quer ligar a este novo casulo usando Cloud Pod Architecture (CPA).

* Os utilizadores finais conectam-se aos seus ambientes de trabalho virtuais através da internet (vs. conectando-se através de um centro de dados no local).

Liga o seu controlador de domínio AD na Rede Virtual Azure com o seu AD no local através do circuito VPN ou ExpressRoute.

Uma variação do exemplo básico pode ser o de apoiar a conectividade dos recursos no local. Por exemplo, os utilizadores acedem a desktops e geram tráfego de aplicações virtuais para desktop ou ligam-se a um pod Horizonte no local usando CPA.

O diagrama mostra como apoiar a conectividade para os recursos no local. Para se ligar à sua rede corporativa à Rede Virtual Azure, precisará de um circuito ExpressRoute.  Também terá de ligar a sua rede corporativa a cada uma das nuvems privadas e SDDCs usando o ExpressRoute Global Reach.  Permite a conectividade do SDDC ao circuito ExpressRoute e aos recursos no local. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Ligue a sua rede corporativa a uma Rede Virtual Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Várias cápsulas Horizon na Azure VMware Solution em várias regiões

Outro cenário é escalar horizonte através de várias cápsulas.  Neste cenário, implanta-se duas cápsulas Horizon em duas regiões diferentes e federa-as usando CPA. É semelhante à configuração da rede no exemplo anterior, mas com algumas ligações trans-regionais adicionais. 

Você vai ligar a Rede Virtual Azure em cada região às nuvens/SDDCs privados na outra região. Permite que os servidores de ligação Horizon parte da federação CPA se conectem a todos os ambientes de trabalho sob gestão. Adicionar nuvens/SDDCs adicionais a esta configuração permitir-lhe-ia escalar para 24.000 sessões no total. 

Os mesmos princípios aplicam-se se implementar duas cápsulas Horizon na mesma região.  Certifique-se de que implanta a segunda cápsula Horizon numa *rede virtual Azure separada*. Tal como o exemplo de um único pod, pode ligar a sua rede corporativa e o pod no local a este exemplo multi-pod/região usando ExpressRoute e Global Reach. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text=" Várias cápsulas Horizon na Azure VMware Solution em várias regiões" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Solução De Tamanho Azure VMware para implementações horizon 

A metodologia de dimensionamento da Horizon num hospedeiro em execução na Azure VMware Solution é mais simples do que a Horizon no local.  Isto porque o anfitrião Azure VMware Solution está padronizado.  O tamanho exato do hospedeiro ajuda a determinar o número de anfitriões necessários para suportar os seus requisitos de VDI.  É central para determinar o custo por ambiente de trabalho.

### <a name="sizing-tables"></a>Tabelas de dimensionamento

Os requisitos específicos de vCPU/vRAM para desktops virtuais Horizon dependem do perfil de carga de trabalho específico do cliente.   Trabalhe com a sua equipa de vendas da MSFT e da VMware para ajudar a determinar os seus requisitos vCPU/vRAM para os seus ambientes de trabalho virtuais. 

| vCPU por VM | vRAM por VM (GB) | Instância | 100 VMs | 200 VMs | 300 VMs | 400 VMs | 500 VMs | 600 VMs | 700 VMs | 800 VMs | 900 VMs | 1000 VMs | 2000 VMs | 3000 VMs | 4000 VMs | 5000 VMs | 6000 VMs | 6400 VMs |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3.5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3.5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3.5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3.5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Entradas de dimensionamento horizonte

Aqui está o que precisa de reunir para a sua carga de trabalho planeada:

* Número de ambientes de trabalho simultâneos

* VCPU necessário por ambiente de trabalho

* VRAM necessário por ambiente de trabalho

* Armazenamento necessário por ambiente de trabalho

Em geral, as implementações de VDI são restritas ao CPU ou à RAM, o que determina o tamanho do hospedeiro. Vejamos o seguinte exemplo para um tipo de carga de trabalho do LoginVSI Knowledge Worker, validado com testes de desempenho:

* 2.000 implementação simultânea de desktop

* 2vCPU por ambiente de trabalho.

* 4-GB vRAM por ambiente de trabalho.

* 50 GB de armazenamento por ambiente de trabalho

Por exemplo, o número total de hospedeiros aponta para 18, o que produz uma densidade VM-por-hospedeiro de 111.

>[!IMPORTANT]
>As cargas de trabalho dos clientes variam deste exemplo de um Trabalhador do Conhecimento loginVSI. Como parte do planeamento da sua implementação, trabalhe com as suas EE VMware para as suas necessidades específicas de dimensionamento e desempenho. Certifique-se de que realiza o seu próprio teste de desempenho utilizando a carga de trabalho real e planeada antes de finalizar o tamanho do hospedeiro e ajustar-se em conformidade.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Licenciamento horizonte na Azure VMware Solution 

Existem quatro componentes para os custos globais de funcionamento da Horizon na Azure VMware Solution. 

### <a name="azure-vmware-solution-capacity-cost"></a>Custo de capacidade da solução VMware Azure

Para obter informações sobre o preço, consulte a página de preços da [Solução VMware Azure](https://azure.microsoft.com/pricing/details/azure-vmware/)

### <a name="horizon-licensing-cost"></a>Custo de Licenciamento Horizonte

Existem duas licenças disponíveis para utilização com a Solução VMware Azure, que pode ser utilizador simultânea (CCU) ou Utilizador Nomeado (NU):

* Licença de Assinatura Horizonte

* Licença de assinatura universal horizonte

Se apenas implementar a Horizon na Solução VMware Azure num futuro previsível, utilize a Licença de Subscrição Horizonte, uma vez que é um custo mais baixo.

Se implantado na Azure VMware Solution e no local, como acontece com um caso de utilização de recuperação de desastres, escolha a Licença de Subscrição Universal Horizonte. Inclui uma licença vSphere para implantação no local, por isso tem um custo mais elevado.

Trabalhe com a sua equipa de vendas da VMware EUC para determinar o custo de licenciamento da Horizon com base nas suas necessidades.

### <a name="azure-instance-types"></a>Tipos de exemplos Azure

Para compreender os tamanhos das máquinas virtuais Azure que serão necessários para a Infraestrutura Horizonte consulte as diretrizes da VMware que podem ser encontradas [aqui.](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution)

## <a name="references"></a>Referências
[Requisitos do sistema para agente horizonte para linux](https://docs.vmware.com/en/VMware-Horizon/2012/linux-desktops-setup/GUID-E268BDBF-1D89-492B-8563-88936FD6607A.html)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a VMware Horizon na Solução VMware Azure, leia o [VMware Horizon FAQ](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf).
