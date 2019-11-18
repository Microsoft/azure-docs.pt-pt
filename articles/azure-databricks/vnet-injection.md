---
title: Implementar o Azure Databricks na sua rede virtual
description: Este artigo descreve como implantar Azure Databricks em sua rede virtual, também conhecida como injeção de VNet.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 3894904575d545aed0dbfce470247afb145b7590
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129306"
---
# <a name="deploy-azure-databricks-in-your-virtual-network"></a>Implementar o Azure Databricks na sua rede virtual

A implantação padrão do Azure Databricks é um serviço totalmente gerenciado no Azure: todos os recursos do plano de dados, incluindo uma rede virtual (VNet), são implantados em um grupo de recursos bloqueado. No entanto, se você precisar de personalização de rede, poderá implantar Azure Databricks recursos em sua própria rede virtual (também chamada de injeção de VNet) quando o permitir:

* Conecte Azure Databricks a outros serviços do Azure (como o armazenamento do Azure) de maneira mais segura usando pontos de extremidade de serviço.
* Conecte-se a fontes de dados locais para uso com Azure Databricks, aproveitando as rotas definidas pelo usuário.
* Conecte Azure Databricks a uma solução de virtualização de rede para inspecionar todo o tráfego de saída e executar ações de acordo com as regras de permissão e negação.
* Configure Azure Databricks para usar o DNS personalizado.
* Configure as regras do NSG (grupo de segurança de rede) para especificar restrições de tráfego de saída.
* Implante Azure Databricks clusters em sua rede virtual existente.

A implantação de recursos de Azure Databricks em sua própria rede virtual também permite que você aproveite os intervalos de CIDR flexíveis (em qualquer lugar entre/16-/24 para a rede virtual e entre/18-/26 para as sub-redes).

  > [!NOTE]
  > Não é possível substituir a rede virtual para um espaço de trabalho existente. Se o seu espaço de trabalho atual não puder acomodar o número necessário de nós de cluster ativos, crie outro espaço de trabalho em uma rede virtual maior. Siga [estas etapas detalhadas de migração](howto-regional-disaster-recovery.md#detailed-migration-steps) para copiar recursos (notebooks, configurações de cluster, trabalhos) do espaço de trabalho antigo para o novo.

## <a name="virtual-network-requirements"></a>Requisitos de rede virtual

Você pode usar a interface de implantação do espaço de trabalho Azure Databricks no portal do Azure para configurar automaticamente uma rede virtual existente com as sub-redes necessárias, o grupo de segurança de rede e as configurações de lista de permissões, ou pode usar Azure Resource Manager modelos para configurar sua rede virtual e implantar seu espaço de trabalho.

A rede virtual na qual você implanta seu espaço de trabalho Azure Databricks deve atender aos seguintes requisitos:

### <a name="location"></a>Localização

A rede virtual deve residir no mesmo local que o espaço de trabalho Azure Databricks.

### <a name="subnets"></a>Sub-redes

A rede virtual deve incluir duas sub-redes dedicadas a Azure Databricks:

   1. Uma sub-rede privada com um grupo de segurança de rede configurado que permite a comunicação interna do cluster

   2. Uma sub-rede pública com um grupo de segurança de rede configurado que permite a comunicação com o plano de controle de Azure Databricks.

### <a name="address-space"></a>Espaço de endereços

Um bloco CIDR entre/16-/24 para a rede virtual e um bloco CIDR entre/18-/26 para as sub-redes pública e privada.

### <a name="whitelisting"></a>Lista de permissões

Todo o tráfego de entrada e saída entre as sub-redes e o plano de controle de Azure Databricks deve estar na lista de permissões.

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Esta seção descreve como criar um espaço de trabalho do Azure Databricks no portal do Azure e implantá-lo em sua própria rede virtual existente. Azure Databricks atualiza a rede virtual com duas novas sub-redes e grupos de segurança de rede usando os intervalos de CIDR fornecidos por você, as listas de permissões de entrada e saída do tráfego de sub-rede e implanta o espaço de trabalho na rede virtual atualizada.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma rede virtual na qual implantará o espaço de trabalho Azure Databricks. Você pode usar uma rede virtual existente ou criar uma nova, mas a rede virtual deve estar na mesma região que o espaço de trabalho Azure Databricks que você planeja criar. Um intervalo CIDR entre/16-/24 é necessário para a rede virtual.

  > [!Warning]
  > Um espaço de trabalho com uma rede virtual menor – ou seja, um intervalo de CIDR inferior – pode ficar sem endereços IP (espaço de rede) mais rapidamente do que um espaço de trabalho com uma rede virtual maior. Por exemplo, um espaço de trabalho com uma rede virtual/24 e/26 sub-redes pode ter um máximo de 64 nós ativos por vez, enquanto um espaço de trabalho com uma rede virtual/20 e/22 sub-redes pode alojar um máximo de 1024 nós.

  Suas sub-redes serão criadas automaticamente quando você configurar seu espaço de trabalho, e você terá a oportunidade de fornecer o intervalo CIDR para as sub-redes durante a configuração.

## <a name="configure-the-virtual-network"></a>Configurar a rede virtual

1. Na portal do Azure, selecione **+ criar um recurso > Analytics > Azure Databricks** para abrir a caixa de diálogo serviço Azure Databricks.

2. Siga as etapas de configuração descritas na etapa 2: criar um Azure Databricks espaço de trabalho no guia de Introdução e selecione a opção implantar Azure Databricks espaço de trabalho em sua rede virtual.

   ![Criar Azure Databricks serviço](./media/vnet-injection/create-databricks-service.png)

3. Selecione a rede virtual que você deseja usar.

   ![Opções de rede virtual](./media/vnet-injection/select-vnet.png)

4. Forneça intervalos CIDR em um bloco entre/18-/26 para duas sub-redes, dedicadas a Azure Databricks:

   * Uma sub-rede pública será criada com um grupo de segurança de rede associado que permite a comunicação com o plano de controle de Azure Databricks.
   * Uma sub-rede privada será criada com um grupo de segurança de rede associado que permite a comunicação interna do cluster.

5. Clique em **criar** para implantar o espaço de trabalho Azure Databricks na rede virtual.

## <a name="advanced-resource-manager-configurations"></a>Configurações avançadas do Resource Manager

Se você quiser mais controle sobre a configuração da rede virtual – por exemplo, você deseja usar sub-redes existentes, usar grupos de segurança de rede existentes ou criar suas próprias regras de segurança – você pode usar os seguintes modelos de Azure Resource Manager em vez do configuração de rede virtual do portal e implantação do espaço de trabalho.

### <a name="all-in-one"></a>Tudo em um

Para criar uma rede virtual, grupos de segurança de rede e Azure Databricks espaço de trabalho em um, use o [modelo All-in-One para os espaços de trabalho injetados por VNet do databricks](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Ao usar esse modelo, você não precisa fazer nenhuma lista branca manual de tráfego de sub-rede.

### <a name="network-security-groups"></a>Grupos de segurança de rede

Para criar grupos de segurança de rede com as regras necessárias para uma rede virtual existente, use o [modelo de grupo de segurança de rede para injeção de VNet do databricks](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Ao usar esse modelo, você não precisa fazer nenhuma lista branca manual de tráfego de sub-rede.

### <a name="virtual-network"></a>Rede virtual

Para criar uma rede virtual com as sub-redes públicas e privadas apropriadas, use o [modelo de rede virtual para injeção de VNet do databricks](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Se você usar esse modelo sem usar também o modelo de grupos de segurança de rede, será necessário adicionar manualmente as regras de lista de permissões aos grupos de segurança de rede usados com a rede virtual.

### <a name="azure-databricks-workspace"></a>Espaço de trabalho Azure Databricks

Para implantar um espaço de trabalho Azure Databricks em uma rede virtual existente que tenha sub-redes públicas e privadas e grupos de segurança de rede configurados corretamente já configurados, use o [modelo de espaço de trabalho para injeção de VNet do databricks](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Se você usar esse modelo sem usar também o modelo de grupos de segurança de rede, será necessário adicionar manualmente as regras de lista de permissões aos grupos de segurança de rede usados com a rede virtual.

## <a name="whitelisting-subnet-traffic"></a>Tráfego de sub-rede de lista de permissões

Se você não usar os modelos de [portal do Azure](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject#vnet-inject-portal) ou [Azure Resource Manager](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject#vnet-inject-advanced) para criar os grupos de segurança de rede, deverá colocar a lista de permissões manualmente no tráfego a seguir em suas sub-redes.

|Direção|Protocolo|Origem|Porta de origem|Destino|Porta de destino|
|---------|--------|------|-----------|-----------|----------------|
|Entrada|\*|VirtualNetwork|\*|\*|\*|
|Entrada|\*|IP de NAT do plano de controle|\*|\*|22|
|Entrada|\*|IP de NAT do plano de controle|\*|\*|5557|
|Saída|\*|\*|\*|IP do webapp|\*|
|Saída|\*|\*|\*|SQL (marca de serviço)|\*|
|Saída|\*|\*|\*|Armazenamento (marca de serviço)|\*|
|Saída|\*|\*|\*|VirtualNetwork|\*|

O tráfego de sub-rede da lista de permissões usando os seguintes endereços IP. Para SQL (metastore) e armazenamento (artefato e armazenamento de log), você deve usar as marcas de [serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)de armazenamento e SQL.

|Região de Azure Databricks|Serviço|IP público|
|-----------------------|-------|---------|
|EUA Leste|NAT do plano de controle </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|EUA Leste 2|NAT do plano de controle </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|EUA Centro-Norte|NAT do plano de controle </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|EUA Central|NAT do plano de controle </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|EUA Centro-Sul|NAT do plano de controle </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|EUA Oeste|NAT do plano de controle </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|EUA Oeste 2|NAT do plano de controle </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Canadá Central|NAT do plano de controle </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Leste do Canadá|NAT do plano de controle </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Reino Unido Oeste|NAT do plano de controle </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Reino Unido Sul|NAT do plano de controle </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Europa ocidental|NAT do plano de controle </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Europa do Norte|NAT do plano de controle </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Índia Central|NAT do plano de controle </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Sul da Índia|NAT do plano de controle </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Oeste da Índia|NAT do plano de controle </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Sudeste Asiático|NAT do plano de controle </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Ásia Oriental|NAT do plano de controle </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Leste da Austrália|NAT do plano de controle </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Sudeste da Austrália|NAT do plano de controle </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrália Central|NAT do plano de controle </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrália Central 2|NAT do plano de controle </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Leste do Japão|NAT do plano de controle </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Oeste do Japão|NAT do plano de controle </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="workspace-launch-errors"></a>Erros de inicialização do espaço de trabalho

A inicialização de um espaço de trabalho em uma rede virtual personalizada falha na tela de entrada Azure Databricks com o seguinte erro: **"encontramos um erro ao criar seu espaço de trabalho. Verifique se a configuração de rede personalizada está correta e tente novamente. "**

Esse erro é causado por uma configuração de rede que não atende aos requisitos. Confirme que você seguiu as instruções neste tópico quando criou o espaço de trabalho.

### <a name="cluster-creation-errors"></a>Erros de criação de cluster

**Instâncias inacessíveis: os recursos não estavam acessíveis via SSH.**

Causa possível: o tráfego do plano de controle para trabalhadores é bloqueado. Correção garantindo que as regras de segurança de entrada atendam aos requisitos. Se você estiver implantando em uma rede virtual existente conectada à sua rede local, examine a configuração usando as informações fornecidas em conectando seu espaço de trabalho do Azure Databricks à sua rede local.

**Falha de inicialização inesperada: um erro inesperado foi encontrado durante a configuração do cluster. Tente novamente e contate Azure Databricks se o problema persistir. Mensagem de erro interno: tempo limite atingido ao colocar o nó.**

Causa possível: o tráfego de trabalhadores para pontos de extremidade de armazenamento do Azure está bloqueado. Correção garantindo que as regras de segurança de saída atendam aos requisitos. Se você estiver usando servidores DNS personalizados, verifique também o status dos servidores DNS em sua rede virtual.

**Falha ao iniciar o provedor de nuvem: um erro de provedor de nuvem foi encontrado durante a configuração do cluster. Consulte o guia de Azure Databricks para obter mais informações. Código de erro do Azure: AuthorizationFailed/InvalidResourceReference.**

Possível causa: a rede virtual ou as sub-redes não existem mais. Verifique se a rede virtual e as sub-redes existem.

**Cluster encerrado. Motivo: falha de inicialização do Spark: o Spark não pôde iniciar no tempo. Esse problema pode ser causado por um mau funcionamento metastore do Hive, configurações do Spark inválidas ou scripts de inicialização com mau funcionamento. Consulte os logs de driver do Spark para solucionar esse problema e contate o databricks se o problema persistir. Mensagem de erro interno: falha do Spark ao iniciar: o driver falhou ao iniciar no tempo.**

Causa possível: o contêiner não pode se comunicar com a instância de hospedagem ou a conta de armazenamento DBFS. Corrija adicionando uma rota personalizada para as sub-redes para a conta de armazenamento DBFS com o próximo salto sendo a Internet.

### <a name="notebook-command-errors"></a>Erros de comando do notebook

**O comando não está respondendo**

Causa possível: a comunicação de trabalho para o trabalhador é bloqueada. Correção, certificando-se de que as regras de segurança de entrada atendam aos requisitos.

**O fluxo de trabalho de notebook falha com a exceção: com. databricks. Idfluxodetrabalho: org. Apache. http. Conn. ConnectTimeoutException**

Causa possível: o tráfego de trabalhadores para Azure Databricks WebApp é bloqueado. Correção, certificando-se de que as regras de segurança de saída atendam aos requisitos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar dados com o Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
