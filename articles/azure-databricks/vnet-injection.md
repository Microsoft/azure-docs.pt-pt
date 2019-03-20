---
title: Implementar o Azure Databricks na sua rede virtual (pré-visualização)
description: Este artigo descreve como implementar o Azure Databricks para sua rede virtual, também conhecida como injeção de VNet.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: c29d2e1df0979481c0c8a1e1f2cd4d22b013212a
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227711"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>Implementar o Azure Databricks na sua rede virtual (pré-visualização)

A implementação padrão do Azure Databricks é um serviço completamente gerido no Azure: todos os recursos do plano de dados, incluindo uma rede virtual (VNet), são implementados para um grupo de recursos bloqueado. Se necessitar de personalização de rede, no entanto, pode implementar recursos do Azure Databricks na sua própria rede virtual (também denominada VNet injeção), quando permite-lhe:

* Ligar o Azure Databricks para outros serviços do Azure (por exemplo, o armazenamento do Azure) de forma mais segura, através de pontos finais de serviço.
* Ligar a dados no local origens para utilização com o Azure Databricks, tirando partido das rotas definidas pelo utilizador.
* Ligar o Azure Databricks para uma aplicação virtual de rede para inspecionar todo o tráfego de saída e tome medidas de acordo com a permissão e negação regras.
* Configure o Azure Databricks para utilizar o DNS personalizado.
* Configure regras de grupo (NSG) de segurança de rede para especificar restrições de tráfego de saída.
* Implemente clusters do Azure Databricks na sua rede virtual existente.

Implementar recursos do Azure Databricks para sua própria rede virtual também permite-lhe tirar partido de intervalos CIDR flexíveis (em qualquer lugar entre /16-/ 24 para a rede virtual e entre /18-/ 26 para as sub-redes).

  > [!NOTE]
  > Não é possível substituir a rede virtual para uma área de trabalho existente. Se a sua área de trabalho atual não pode acomodar o número necessário de nós de cluster ativo, crie outra área de trabalho numa rede virtual maior. Siga [detalhadas estes passos de migração](howto-regional-disaster-recovery.md#detailed-migration-steps) para copiar recursos (blocos de notas, configurações de cluster, tarefas) de antiga para a nova área de trabalho.

## <a name="virtual-network-requirements"></a>Requisitos de rede virtual

Pode utilizar a interface de implantação de área de trabalho do Azure Databricks no portal do Azure para configurar automaticamente uma rede virtual existente com as sub-redes necessárias, o grupo de segurança de rede e as definições de listas de permissões ou pode utilizar o Azure Resource Manager modelos para configurar a rede virtual e implementar a sua área de trabalho.

A rede virtual que implementar a sua área de trabalho do Azure Databricks para tem de cumprir os seguintes requisitos:

### <a name="location"></a>Localização

A rede virtual tem de residir na mesma localização que a área de trabalho do Azure Databricks.

### <a name="subnets"></a>Sub-redes

A rede virtual tem de incluir duas sub-redes dedicadas ao Azure Databricks:

   1. Uma sub-rede privada com um grupo de segurança de rede configurado que permite a comunicação interna para o cluster

   2. Uma sub-rede pública com um grupo de segurança de rede configurado que permite a comunicação com o plano de controlo do Azure Databricks.

### <a name="address-space"></a>Espaço de endereços

Um bloco CIDR entre /16-/ 24, para a rede virtual e um bloco CIDR entre /18-/26 para as sub-redes privadas e públicas.

### <a name="whitelisting"></a>Lista de permissões

Todo o tráfego de entrada e saído entre as sub-redes e o plano de controlo do Azure Databricks tem de estar na lista de permissões.

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Esta secção descreve como criar uma área de trabalho do Azure Databricks no portal do Azure e implementá-lo em sua própria rede virtual existente. O Azure Databricks atualiza a rede virtual com duas novas sub-redes e grupos de segurança de rede através de intervalos CIDR fornecidos por si, listas de permissões de entrada e saída da sub-rede tráfego e implementa a área de trabalho na rede virtual atualizada.

## <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma rede virtual nos quais implementou a área de trabalho do Azure Databricks. Pode utilizar uma rede virtual existente ou crie um novo, mas a rede virtual tem de estar na mesma região que a área de trabalho do Azure Databricks que planeia criar. Um intervalo CIDR entre /16 /24 é necessário para a rede virtual.

  > [!Warning]
  > Uma área de trabalho com uma rede virtual menor – ou seja, a mais baixa intervalo CIDR – pode ficar sem endereços IP (espaço de rede) mais rapidamente do que uma área de trabalho com uma rede virtual maior. Por exemplo, uma área de trabalho com um /24 rede virtual e /26 sub-redes podem ter um máximo de 64 nós ativos por vez, ao passo que uma área de trabalho com um /20 rede virtual e /22 sub-redes podem alojar um máximo de nós de 1024.

  As sub-redes serão criadas automaticamente quando configurar a sua área de trabalho, e terá a oportunidade de fornecer o intervalo CIDR para as sub-redes durante a configuração.

## <a name="configure-the-virtual-network"></a>Configurar a rede virtual

1. No portal do Azure, selecione **+ criar um recurso > Analytics > Azure Databricks** para abrir a caixa de diálogo de serviço do Azure Databricks.

2. Siga os passos de configuração descritos no passo 2: Criar uma área de trabalho do Azure Databricks no guia de introdução e selecione a área de trabalho do Azure Databricks implementar na sua opção de rede Virtual.

   ![Criar o serviço do Azure Databricks](./media/vnet-injection/create-databricks-service.png)

3. Selecione a rede virtual que pretende utilizar.

   ![Opções de rede virtual](./media/vnet-injection/select-vnet.png)

4. Forneça os intervalos CIDR num bloco entre /18-/26 para duas sub-redes, dedicadas ao Azure Databricks:

   * Será criada uma sub-rede pública com um grupo de segurança de rede associado que permite a comunicação com o plano de controlo do Azure Databricks.
   * Com um grupo de segurança de rede associado que permite a comunicação interna para o cluster será criado um sub-rede privada.

5. Clique em **criar** para implementar a área de trabalho do Azure Databricks para a rede virtual.

## <a name="advanced-resource-manager-configurations"></a>Configurações de Gestor de recursos avançados

Se quiser mais controle sobre a configuração da rede virtual – por exemplo, pretender utilizar sub-redes existentes, utilize grupos de segurança de rede existente ou criar suas próprias regras de segurança – pode utilizar os seguintes modelos do Azure Resource Manager, em vez do implementação da área de trabalho e configuração do portal de rede virtual.

### <a name="all-in-one"></a>Todos em um

Para criar uma rede virtual, grupos de segurança de rede e área de trabalho do Azure Databricks todos em um, utilize o [tudo-em-um modelo para áreas de trabalho do Databricks VNet Injetado](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Quando utiliza este modelo, não terá de fazer qualquer lista de permissões manual de tráfego de sub-rede.

### <a name="network-security-groups"></a>Grupos de segurança de rede

Para criar grupos de segurança de rede com as regras necessárias para uma rede virtual existente, utilize o [modelo de grupo de segurança de rede para a Injeção de VNet do Databricks](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection).

Quando utiliza este modelo, não terá de fazer qualquer lista de permissões manual de tráfego de sub-rede.

### <a name="virtual-network"></a>Rede virtual

Para criar uma rede virtual com as sub-redes privadas e públicas adequadas, utilize o [modelo de rede Virtual para a Injeção de VNet do Databricks](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Se utilizar este modelo sem também utilizar o modelo de grupos de segurança de rede, tem de adicionar manualmente regras de inclusão para os grupos de segurança de rede que utiliza com a rede virtual.

### <a name="azure-databricks-workspace"></a>Área de trabalho do Azure Databricks

Para implementar uma área de trabalho do Azure Databricks para uma rede virtual existente que tenha sub-redes públicas e privadas e já configurados a grupos de segurança de rede configurado corretamente, utilize o [modelo de área de trabalho para a Injeção de VNet do Databricks](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Se utilizar este modelo sem também utilizar o modelo de grupos de segurança de rede, tem de adicionar manualmente regras de inclusão para os grupos de segurança de rede que utiliza com a rede virtual.

## <a name="whitelisting-subnet-traffic"></a>Tráfego de sub-rede de listas de permissões

Se não utilizar o [portal do Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) ou [modelos Azure Resource Manager](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) para criar a rede com grupos de segurança, tem manualmente o seguinte tráfego de lista de permissões nas suas sub-redes.

|Direção|Protocolo|Origem|Porta de origem|Destino|Porta de destino|
|---------|--------|------|-----------|-----------|----------------|
|Entrada|\*|VirtualNetwork|\*|\*|\*|
|Entrada|\*|IP do NAT de plano de controlo|\*|\*|22|
|Entrada|\*|IP do NAT de plano de controlo|\*|\*|5557|
|Saída|\*|\*|\*|IP da aplicação Web|\*|
|Saída|\*|\*|\*|SQL (etiqueta de serviço)|\*|
|Saída|\*|\*|\*|Armazenamento (etiqueta de serviço)|\*|
|Saída|\*|\*|\*|VirtualNetwork|\*|

Tráfego de sub-rede de lista branca com o IP seguinte endereços. Para SQL (metastore) e armazenamento (armazenamento de artefactos e de log), deve usar o Sql e o armazenamento [etiquetas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

|Região do Azure Databricks|Serviço|IP público|
|-----------------------|-------|---------|
|EUA Leste|Plano de controlo NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|EUA Leste 2|Plano de controlo NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|EUA Centro-Norte|Plano de controlo NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|EUA Central|Plano de controlo NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|EUA Centro-Sul|Plano de controlo NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|EUA Oeste|Plano de controlo NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|EUA Oeste 2|Plano de controlo NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Canadá Central|Plano de controlo NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Leste do Canadá|Plano de controlo NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Reino Unido Oeste|Plano de controlo NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Reino Unido Sul|Plano de controlo NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Europa Ocidental|Plano de controlo NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Europa do Norte|Plano de controlo NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Índia Central|Plano de controlo NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Sul da Índia|Plano de controlo NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Oeste da Índia|Plano de controlo NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Sudeste Asiático|Plano de controlo NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Ásia Oriental|Plano de controlo NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Leste da Austrália|Plano de controlo NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Sudeste da Austrália|Plano de controlo NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrália Central|Plano de controlo NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrália Central 2|Plano de controlo NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Leste do Japão|Plano de controlo NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Oeste do Japão|Plano de controlo NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="workspace-launch-errors"></a>Erros de lançamento de área de trabalho

Iniciar uma área de trabalho numa rede virtual personalizada falha sobre o Azure Databricks ecrã com o seguinte erro de início de sessão: **"Que encontramos um erro ao criar a sua área de trabalho. Certifique-se de que a configuração de rede personalizada está correta e tente novamente."**

Este erro é causado por uma configuração de rede não atende aos requisitos. Confirme que seguiu as instruções neste tópico quando criou a área de trabalho.

### <a name="cluster-creation-errors"></a>Erros de criação do cluster

**Instâncias inacessíveis: Recursos não eram acessíveis através de SSH.**

Causa possível: tráfego de plano de controlo para funções de trabalho é bloqueado. Corrigi, garantindo que as regras de segurança de entrada atende aos requisitos. Se estiver a implementar uma rede virtual existente, ligada à sua rede no local, reveja a configuração com as informações fornecidas na sua área de trabalho do Azure Databricks a ligar à sua rede no local.

**Falha de inicialização inesperado: Um erro inesperado foi encontrado ao configurar o cluster. Tente novamente e se o problema persistir, contacte do Azure Databricks. Mensagem de erro interno: Tempo limite durante a colocação de nó.**

Causa possível: o tráfego dos trabalhadores para pontos finais de armazenamento do Azure é bloqueado. Corrigi, garantindo que as regras de segurança de saída atende aos requisitos. Se estiver a utilizar servidores DNS personalizados, também verificar o estado dos servidores DNS na sua rede virtual.

**Falha de lançamento de fornecedor de cloud: Foi encontrado um erro de fornecedor de cloud durante a configuração do cluster. Consulte o Guia do Azure Databricks para obter mais informações. Código de erro do Azure: AuthorizationFailed/InvalidResourceReference.**

Causa possível: a sub-redes ou a rede virtual já não existe. Certifique-se que a rede virtual e sub-redes existem.

**Terminada a cluster. Razão: Falha de inicialização do Spark: Spark não foi possível iniciar no tempo. Este problema pode ser causado por um metastore Hive com funcionamento incorreto, configurações de Spark inválidas ou scripts de inicialização com funcionamento incorreto. Consulte os registos de controlador do Spark para resolver este problema e, se o problema persistir, contacte Databricks. Mensagem de erro interno: Spark falhou ao iniciar: Controlador falha ao iniciar no tempo.**

Causa possível: Contentor não é possível comunicar com a instância de alojamento ou conta de armazenamento DBFS. Corrigi-se ao adicionar uma rota personalizada para as sub-redes para a conta de armazenamento DBFS com o que está a ser Internet de salto seguinte.

### <a name="notebook-command-errors"></a>Erros de comando do bloco de notas

**Comando parou**

Causa possível: a comunicação de trabalho para o trabalho está bloqueada. Corrigi, certificar-se de que as regras de segurança de entrada atender aos requisitos.

**Falha de fluxo de trabalho do bloco de notas com a exceção: com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException**

Causa possível: o tráfego de funções de trabalho para a aplicação Web do Azure Databricks está bloqueado. Corrigi, certificar-se de que as regras de segurança de saída atender aos requisitos.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar dados com o Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
