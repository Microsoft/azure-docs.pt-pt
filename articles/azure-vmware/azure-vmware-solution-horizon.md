---
title: Implementar Horizonte na Solução VMware Azure
description: Saiba como implementar o VMware Horizon na Solução VMware Azure.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: bda4be049e360670cb7038bfbb3070c2a5f262c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729054"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Implementar Horizonte na Solução VMware Azure 

>[!NOTE]
>Este documento centra-se no produto VMware Horizon. Isto é anteriormente conhecido como Horizonte 7 antes da mudança do nome do produto de Horizonte 7 para Horizonte. Horizonte é uma solução diferente da Horizon Cloud no Azure, embora existam alguns componentes partilhados. As principais vantagens da Solução Azure VMware incluem tanto um método de dimensionamento mais simples como que a gestão da VMware Cloud Foundation está integrada no portal Azure.

[A VMware Horizon](https://www.vmware.com/products/horizon.html)® é uma plataforma virtual de desktop e aplicações que funciona no centro de dados e fornece ti com uma gestão simples e centralizada. Fornece desktops e aplicações virtuais aos utilizadores finais em qualquer dispositivo, em qualquer lugar. O Horizonte permite criar e intermediar ligações a desktops virtuais do Windows, desktops virtuais Linux, Aplicações, desktops e máquinas físicas do Remote Desktop Server (RDS).

Aqui, focamo-nos especificamente na implementação da Horizon na Azure VMware Solution. Para obter informações gerais sobre a VMware Horizon, consulte a documentação de produção da Horizon:

* [O que é VMware Horizon?](https://www.vmware.com/products/horizon.html)

* [Saiba mais sobre a VMware Horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Arquitetura de Referência Horizonte](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Com a introdução da Horizon na Azure VMware Solution, existem agora duas soluções virtual de Infraestruturas de Ambiente de Trabalho (VDI) na plataforma Azure. O diagrama seguinte resume as diferenças-chave a um nível elevado.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="As diferenças entre Horizonte na Solução VMware Azure e Nuvem Horizon em Azure" border="false":::

As versões Horizonte 2006 e posteriores na linha de lançamento do Horizonte 8 suportam a implementação no local e a implementação da Solução VMware Azure. Existem algumas funcionalidades da Horizon que são suportadas no local, mas não na Azure VMware Solution. São também apoiados produtos adicionais no ecossistema Horizon. Para obter informações, consulte [paridade de características e interoperabilidade.](https://kb.vmware.com/s/article/80850)

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Implementar Horizonte numa nuvem híbrida

Pode implementar a Horizon num ambiente de nuvem híbrida quando utilizar o Horizon Cloud Pod Architecture (CPA) para interligar centros de dados no local e centros de dados Azure. O CPA é normalmente usado para aumentar a sua implantação, construir uma nuvem híbrida e fornecer redundância para a Continuidade do Negócio e Recuperação de Desastres. Para uma discussão aprofundada sobre a orientação de continuidade de negócios da VMware Horizon, consulte [a Expansão do Horizonte 7 Ambientes.](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874)

>[!IMPORTANT]
>A CPA não é uma implantação estendida; cada cápsula Horizon é distinta, e todos os Servidores de Conexão que pertencem a cada uma das cápsulas individuais são obrigados a ser localizados num único local e executados no mesmo domínio de transmissão a partir de uma perspetiva de rede.

Tal como no local ou num centro de dados privado, a Horizon pode ser implantada numa nuvem privada Azure VMware Solution. Discutiremos as principais diferenças na implementação da Horizon no local e na Azure VMware Solution nas seguintes secções.

A nuvem privada Azure é conceptualmente a mesma que o VMware SDDC, um termo tipicamente usado na documentação horizonte. O restante deste documento utilizará os termos Azure private cloud e VMware SDDC intercambiáveis.

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

Um design de arquitetura horizonte típico usa uma estratégia de pod e bloco. Um bloco é um único vCenter, enquanto vários blocos combinados fazem uma vagem. Um pod Horizon é uma unidade de organização determinada pelos limites de escalabilidade horizonte. Cada cápsula Horizon tem um portal de gestão separado, e, portanto, uma prática de design padrão é para minimizar o número de cápsulas.

Cada nuvem tem o seu próprio esquema de conectividade de rede. Combinada com a rede VMware SDDC / NSX Edge, a conectividade da rede Azure VMware Solution apresenta requisitos únicos para a implementação do Horizon que é diferente do local.

Cada nuvem privada Azure / SDDC é capaz de lidar com 4.000 sessões de desktop ou app, que assumem o seguinte:

* O tráfego de carga de trabalho alinha-se com o do perfil do trabalhador de tarefas LoginVSI.

* Apenas o tráfego de protocolo é considerado, nenhum dado do utilizador.

* O NSX Edge está configurado para ser grande.

>[!NOTE]
>O seu perfil de carga de trabalho e as suas necessidades podem ser diferentes, pelo que os resultados podem variar em função do seu caso de utilização. Os volumes de Dados do Utilizador podem reduzir os limites de escala no contexto da sua carga de trabalho. Tamanho e planeie a sua implantação em conformidade. Para obter mais informações, consulte as diretrizes de dimensionamento nos [anfitriões da Solução VMware Size Azure para implementações horizon.](#size-azure-vmware-solution-hosts-for-horizon-deployments)

Dado o limite máximo de nuvem privada Azure / SDDC, recomendamos uma arquitetura de implantação onde os Servidores de Ligação Horizonte e os Gateways de Acesso Unificados de VMware (UAGs) estão a funcionar dentro da Rede Virtual Azure. Isto efetivamente transforma cada nuvem privada Azure / SDDC em um bloco. Isto, por sua vez, maximiza a escalabilidade da Horizon em execução na Azure VMware Solution.

A ligação da Rede Virtual Azure às nuvens privadas Azure /SDDCs deve ser configurada com ExpressRoute FastPath. O diagrama seguinte mostra uma implantação básica da cápsula Horizon.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="As diferenças entre Horizonte na Solução VMware Azure e Nuvem Horizon em Azure" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Conectividade de rede para escalar Horizonte na Solução VMware Azure

Esta secção estabelece a arquitetura da rede a um nível elevado para escalar horizonte na Solução VMware Azure com alguns exemplos de implementação comuns. O foco aqui é especificamente em elementos críticos de networking.

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Cápsula Single Horizon na Solução VMware Azure

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="As diferenças entre Horizonte na Solução VMware Azure e Nuvem Horizon em Azure" border="false":::

Uma única cápsula Horizon é o cenário de implantação mais simples para a frente. Neste exemplo, decide que pretende implantar apenas uma cápsula Horizon na região leste dos EUA. Uma vez que cada nuvem privada / SDDC é estimado para lidar aproximadamente com o tráfego de 4.000 sessões de desktop, para implementar o tamanho máximo do pod horizon, você pode planear a implantação de até três nuvens privadas / SDDCs.

Assim, neste exemplo, em combinação com as máquinas virtuais de infraestrutura Horizon (VMs) implantadas na Rede Virtual Azure, pode chegar às 12.000 sessões por cápsula Horizon com base na sua carga de trabalho e necessidades de dados. A ligação entre cada nuvem privada e SDDC à Rede Virtual Azure é o ExpressRoute Fast Path, de modo a que não seja necessário tráfego este-oeste entre nuvens privadas.

Os pressupostos-chave para este exemplo de implantação básica incluem que:

* Você não tem um pod Horizon no local que você quer ligar a este novo casulo usando Cloud Pod Architecture (CPA).

* Os utilizadores finais ligam-se aos seus ambientes de trabalho virtuais através da internet (vs. ligação através de um centro de dados no local).

Neste exemplo básico, pode ligar o seu controlador de domínio AD na Rede Virtual Azure com o seu Ative Directory no local via VPN ou um circuito ExpressRoute.

Uma variação do exemplo básico discutido poderá ser o de apoiar a conectividade dos recursos no local. Isto pode ser utilizadores a aceder em ambientes de trabalho e a gerar tráfego de aplicações virtuais para desktop ou a ligar-se a um pod horizonte no local utilizando O CPA.

O diagrama abaixo mostra como podes fazer isso.Para ligar a sua rede corporativa à Rede Virtual Azure, necessitará de um ExpressRoute.Também terá de ligar a sua rede corporativa a cada uma das nuvems privadas /SDDCs utilizando o Global Reach, que permite a conectividade do SDDC ao ExpressRoute e aos recursos no local.

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="As diferenças entre Horizonte na Solução VMware Azure e Nuvem Horizon em Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Várias cápsulas Horizon na Azure VMware Solution em várias regiões

Para mais um exemplo de pod horizon, vamos olhar para um exemplo que mostra escalar horizonte através de vários Pods.Neste exemplo, está a implantar duas cápsulas Horizon em duas regiões diferentes e a federa-las usando CPA.A configuração da rede é como o exemplo anterior, com algumas ligações trans-regionais adicionais. 

Você precisará ligar a Rede Virtual Azure em cada região às nuvens privadas /SDDCs na outra região, permitindo que servidores de conexão Horizon que fazem parte da federação CPA se conectem a todos os desktops sob gestão.Adicionar nuvens privadas adicionais / SDDCs a esta configuração permitir-lhe-ia escalar para 24.000 sessões no total. 

Embora este exemplo mostre várias regiões, o mesmo princípio aplicar-se-ia se pretendesse implantar duas cápsulas Horizon na mesma região. Note que você precisa de se certificar de que a segunda cápsula Horizon é implantada numa *rede virtual Azure separada*.Finalmente, tal como faria no exemplo anterior do Pod, pode ligar a sua rede corporativa e o pod no local a este exemplo multi-pod/região utilizando o cliente ExpressRoute e Global Reach.

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="As diferenças entre Horizonte na Solução VMware Azure e Nuvem Horizon em Azure" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Solução De Tamanho Azure VMware para implementações horizon 

A metodologia de dimensionamento da Horizon num hospedeiro em execução na Azure VMware Solution é mais simples do que a Horizon on-in, uma vez que a instância de anfitrião da Azure VMware Solution é padronizada. O dimensionamento preciso do anfitrião ajudará a determinar o número de anfitriões necessários para suportar os seus requisitos de VDI e é fundamental para determinar o custo por ambiente de trabalho.

### <a name="azure-vmware-solution-host-instance"></a>Exemplo de anfitrião da Solução VMware Azure

* PowerEdge R640 Server - DSS RESTRITO

* 36 núcleos \@ 2.3GHz

* RAM de 576 GB

* Controlador HBA330 12 Gbps SAS HBA (NÃO-RAID)

* 1.92 TB SSD SATA Mix Use 6 Gbps 512 2.5in Hot-plug AG Drive, 3 DWPD, 10512 TBW

* Intel 1.6 TB, NVMe, Mixed Use Express Flash, 2.5 SFF Drive, U.2, P4600 com Carrier

* 2 vSAN Grupos de discos: 1,6 x 4 (1,92 TB)

### <a name="horizon-sizing-inputs"></a>Entradas de dimensionamento horizonte

Descubra o seguinte para a sua carga de trabalho planeada:

* Número de ambientes de trabalho simultâneos

* VCPU necessário por ambiente de trabalho

* VRAM necessário por ambiente de trabalho

* Armazenamento necessário por ambiente de trabalho

Em geral, as implementações de VDI são restritas ao CPU ou à RAM, uma vez que esses fatores determinarão o tamanho do hospedeiro. Vejamos o seguinte exemplo para um tipo de carga de trabalho do LoginVSI Knowledge Worker, validado com testes de desempenho:

* 2.000 implementação simultânea de desktop

* 2vCPU por ambiente de trabalho.

* 4 GB vRAM por ambiente de trabalho.

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

Se implementar tanto a Horizon na Solução VMware Azure como no local, como acontece com um caso de utilização de recuperação de desastres, então escolha a Licença de Subscrição Universal Horizonte. A licença Universal é um custo mais elevado porque inclui uma licença vSphere para implantação no local.

Trabalhe com a sua equipa de vendas da VMware EUC para determinar o custo de licenciamento da Horizon com base nas suas necessidades.

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Custo da infraestrutura Horizonte VMs na Rede Virtual Azure

Com base na arquitetura de implementação padrão, os VMs de infraestrutura Horizon são compostos por Servidores de Conexão, UAGs, Gestores de Volume de Aplicações e são implantados na Rede Virtual Azure do cliente. Casos nativos adicionais do Azure são necessários para suportar serviços de Alta Disponibilidade (HA), Microsoft SQL ou Microsoft Ative Directory (AD) no Azure. Segue-se uma lista de casos Azure com base num exemplo de implementação de 2.000 desktops. 

| Componente de infraestrutura horizonte | Exemplo de Azure | Número de casos necessários (para 2.000 desktops)    | Comentário  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| Servidor de Conexão                | D4sv3          | 2       | *Inclui 1 instância para HA*             |    
| UAG                              | F2sv2          | 2       | *Inclui 1 instância para HA*             |
| Gestor de Volumes de Aplicativos              | D4sv3          | 2       | *Inclui 1 instância para HA*             |
| Conector de nuvem                  | D4sv3          | 1       |                                          |
| Controlador de anúncios                    | D4sv3          | 2       | *Opção de utilização do serviço MSFT AD no Azure* |
| Base de Dados MS-SQL                  | D4sv3          | 2       | *Opção de utilizar o serviço SQL em Azure*     |
| Partilha de ficheiros do Windows               | D4sv3          |         | *Opcional*                               |

O custo da infraestrutura VM ascende a \$ 0,36 por utilizador por mês para a implantação de 2.000 desktops no exemplo acima. Note-se que este exemplo utiliza preços de instância do US East Azure a partir de junho de 2020. O seu preço pode variar dependendo da região, opções selecionadas e tempo.
