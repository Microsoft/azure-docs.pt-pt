---
title: Ressarça os seus dados para Azure com Veeam
titleSuffix: Azure Storage
description: Fornece uma visão geral dos fatores a considerar e passos a seguir para usar o Azure como alvo de armazenamento e localização de recuperação para o Veeam Backup and Recovery
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 0b8bc0defd3314fcff691a049323201732644ff3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589910"
---
# <a name="backup-to-azure-with-veeam"></a>Backup para Azure com Veeam

Este artigo ajuda-o a integrar uma infraestrutura Veeam com armazenamento Azure Blob. Inclui pré-requisitos, considerações, implementação e orientação operacional. Este artigo aborda a utilização do Azure como alvo de backup fora do local e um local de recuperação se ocorrer um desastre, o que impede o funcionamento normal dentro do seu local principal.

> [!NOTE]
> Veeam também oferece uma solução de tempo de recuperação mais baixa (RTO), replicação veeam. Esta solução permite-lhe ter um VM de espera que pode ajudá-lo a recuperar mais rapidamente em caso de desastre num ambiente de produção Azure. Veeam também tem ferramentas dedicadas para apoiar os recursos do Azure e do Office 365. Estas capacidades estão fora do âmbito deste documento.

## <a name="reference-architecture"></a>Arquitetura de referência

O diagrama seguinte fornece uma arquitetura de referência para as instalações de Azure e in-Azure.

![Veeam a Azure referenciar o diagrama de arquitetura.](../media/veeam-architecture.png)

A sua implementação veeam existente pode facilmente integrar-se com o Azure adicionando uma conta de armazenamento Azure, ou várias contas, como um repositório de backup em nuvem. Veeam também permite que você recupere backups de instalações dentro de Azure dando-lhe um site de recuperação a pedido em Azure.

## <a name="veeam-interoperability-matrix"></a>Matriz de interoperabilidade de Veeam

| Carga de trabalho | Armazenamento de GPv2 e Blob | Suporte de nível legal | Suporte de nível de arquivo | Suporte familiar data box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| VMs/dados no local | v10a | v10a | N/D | 10a<sup>*</sup> |
| VMs do Azure | v10a | v10a | N/D | 10a<sup>*</sup> |
| Blob do Azure | v10a | v10a | N/D | 10a<sup>*</sup> |
| Ficheiros do Azure | v10a | v10a | N/D | 10a<sup>*</sup> |

<sup>*</sup>Suporte de backup e replicação de Veeam REST API apenas para Azure Data Box. Portanto, o disco de caixa de dados Azure não é suportado.

## <a name="before-you-begin"></a>Antes de começar

Um pequeno planeamento inicial irá ajudá-lo a usar o Azure como alvo de backup e local de recuperação.

### <a name="get-started-with-azure"></a>Introdução ao Azure

A Microsoft oferece uma estrutura a seguir para começar com o Azure. O [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) é uma abordagem detalhada da transformação digital empresarial e guia abrangente para planear uma adoção em nuvem de nível de produção. O CAF inclui um Guia de [Configuração Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) passo a passo para ajudá-lo a levantar-se e a correr de forma rápida e segura. Pode encontrar uma versão interativa no [portal Azure.](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade) Você encontrará arquiteturas de amostra, boas práticas específicas para implementar aplicações, e recursos de formação gratuitos para colocá-lo no caminho para a experiência Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Considere a rede entre a sua localização e a Azure

Seja utilizando recursos em nuvem para executar a produção, teste e desenvolvimento, ou como um site de backup e recuperação, é importante entender as suas necessidades de largura de banda para sementeira inicial de backup e para transferências diárias em curso.

A Azure Data Box fornece uma forma de transferir a sua linha de base inicial de backup para Azure sem precisar de mais largura de banda. Isto é útil se a transferência de base for estimada para demorar mais tempo do que você pode tolerar. Pode utilizar o estimador de Transferência de Dados quando criar uma conta de armazenamento para estimar o tempo necessário para transferir a sua cópia de segurança inicial.

![Mostra o estimador de transferência de dados do Azure Storage no portal.](../media/az-storage-transfer.png)

Lembre-se, você precisará de capacidade de rede suficiente para suportar transferências diárias de dados dentro da janela de transferência necessária (janela de backup) sem afetar aplicações de produção. Esta secção descreve as ferramentas e técnicas disponíveis para avaliar as necessidades da sua rede.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Determine a largura de banda que vai precisar

Estão disponíveis várias opções de avaliação para determinar a taxa de variação e o tamanho total do conjunto de backup para a transferência inicial da linha de base para o Azure. Aqui estão alguns exemplos de ferramentas de avaliação e reporte:

- [Rio MiTrend](https://mitrend.com/)
- [Aptos são](https://www.veritas.com/insights/aptare-it-analytics)
- [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Determinar largura de banda de internet não utilizada

É importante saber quanto largura de banda tipicamente não utilizada (ou *cabeceira)* você tem disponível no dia-a-dia. Isto ajuda-o a avaliar se pode cumprir os seus objetivos para:

- tempo inicial para carregar quando não estiver a usar a Caixa de Dados do Azure para sementeira offline
- completar backups diários com base na taxa de alteração identificada anteriormente e na sua janela de backup

Utilize os seguintes métodos para identificar a sala de entrada de largura de banda que os seus backups para Azure são livres de consumir.

- Se é um cliente Azure ExpressRoute existente, veja a [utilização](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) do circuito no portal Azure.
- Contacte o seu ISP. Devem poder partilhar relatórios que mostrem a sua utilização diária e mensal existente.
- Existem várias ferramentas que podem medir a utilização monitorizando o tráfego da sua rede ao nível do router/switch. Incluem-se:

  - [Pacote de analisador de largura de banda solarwinds](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Assistente de Rede Cisco](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Escolha as opções de armazenamento certas

Quando utilizar o Azure como alvo de reserva, utilizará o [armazenamento Azure Blob.](../../../../blobs/storage-blobs-introduction.md) O armazenamento de blob é a solução de armazenamento de objetos da Microsoft. O armazenamento de blob é otimizado para armazenar quantidades massivas de dados não estruturados, que são dados que não aderem a nenhum modelo ou definição de dados. Além disso, o Azure Storage é durável, altamente disponível, seguro e escalável. Pode selecionar o armazenamento certo para a sua carga de trabalho para fornecer o [nível de resiliência](../../../../common/storage-redundancy.md) para atender às suas SLAs internas. O armazenamento de blob é um serviço pay-per-use. É [cobrado mensalmente](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) pela quantidade de dados armazenados, acedendo a esses dados, e no caso de níveis de arquivo e frescos, um período mínimo de retenção exigido. As opções de resiliência e tiering aplicáveis aos dados de backup são resumidas nas seguintes tabelas.

**Opções de resiliência de armazenamento de bolhas:**

|  |Localmente redundante  |Zona redundante  |Geo-redundante  |Zona-de-zona-redundante  |
|---------|---------|---------|---------|---------|
|**Eficaz # de cópias**     | 3         | 3         | 6         | 6 |
|**# de zonas de disponibilidade**     | 1         | 3         | 2         | 4 |
|**# de região** s     | 1         | 1         | 2         | 2 |
|**Falha manual na região secundária**     | N/D         | N/D         | Sim         | Yes |

**Camadas de armazenamento de bolhas:**

|  | Nível quente   |Nível fresco   | Nível de arquivo |
| ----------- | ----------- | -----------  | -----------  |
| **Disponibilidade** | 99,9%         | 99%         | Offline      |
| **Taxas de utilização** | Custos de armazenamento mais elevados, menor acesso e custos de transação | Custos de armazenamento mais baixos, acesso mais elevado e custos de transação | Custos de armazenamento mais baixos, acesso mais elevado e custos de transação |
| **Retenção de dados mínimos necessária** | ND | 30 dias | 180 dias |
| **Latência (tempo para primeiro byte)** | Milissegundos | Milissegundos | Horas |

#### <a name="sample-backup-to-azure-cost-model"></a>Cópia de apoio ao modelo de custos da Azure

Com o pay-per-use pode ser assustador para os clientes que são novos na nuvem. Enquanto paga apenas pela capacidade utilizada, também paga por transações (ler e escrever) e [saída para os dados lidos](https://azure.microsoft.com/pricing/details/bandwidth/) no seu ambiente no local quando a [Rota Expresso Azure direta local ou Rota Expresso plano de dados ilimitados](https://azure.microsoft.com/pricing/details/expressroute/) estão em uso onde a saída de dados do Azure está incluída. Pode utilizar a [Calculadora de Preços Azure](https://azure.microsoft.com/pricing/calculator/) para realizar a análise "e se". Pode basear a análise nos preços da lista ou nos [preços de capacidade reservada de armazenamento Azure,](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)que podem proporcionar até 38% de poupança. Aqui está um exercício de preços de exemplo para modelar o custo mensal de apoio ao Azure. Este é apenas um exemplo. *O seu preço pode variar devido a atividades não capturadas aqui.*

|Fator de custo  |Custo mensal  |
|---------|---------|
|100 TB de dados de backup no armazenamento fresco     |$1556.48         |
|2 TB de novos dados escritos por dia x 30 dias     |$72 em transações          |
|Total estimado mensal     |$1628.48         |
|---------|---------|
|Uma vez restabelecendo 5 TB para as instalações através da internet pública   | $527.26         |

> [!Note]
> Esta estimativa foi gerada na Calculadora de Preços Azure utilizando preços de Pagamento leste dos EUA e baseia-se no padrão veeam de 256 kb tamanho para transferências WAN. Este exemplo pode não ser aplicável às suas necessidades.

## <a name="implementation-guidance"></a>Orientação de implementação

Esta secção fornece um breve guia para como adicionar O Armazenamento Azure a uma implantação veeam no local. Para obter considerações detalhadas sobre orientação e planeamento, consulte o [Guia de Backup da Cloud Veeam.](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100)

1. Abra o portal Azure e procure contas **de armazenamento.** Também pode clicar no ícone de serviço predefinido.

      ![Mostra a adição de uma conta de armazenamento no portal Azure.](../media/azure-portal.png)

      ![Mostra onde escreveu armazenamento na caixa de pesquisa do portal Azure.](../media/locate-storage-account.png)

2. Selecione **Criar** para adicionar uma conta. Selecione ou crie um grupo de recursos, forneça um nome único, escolha a região, selecione desempenho **standard,** deixe sempre o tipo de conta como **Storage V2,** escolha o nível de replicação que vai ao encontro dos seus SLAs, e o nível padrão o seu software de backup será aplicado. Uma conta de Armazenamento Azure disponibiliza níveis quentes, frescos e de arquivo numa única conta e as políticas veeam permitem-lhe utilizar vários níveis para gerir eficazmente o ciclo de vida dos seus dados.

    ![Mostra as definições da conta de armazenamento no portal](../media/account-create-1.png)

3. Mantenha as opções de rede padrão por enquanto e passe para a **proteção de dados**. Aqui, pode optar por permitir a eliminação suave, o que lhe permite recuperar um ficheiro de backup acidentalmente eliminado dentro do período de retenção definido e oferece proteção contra a eliminação acidental ou maliciosa.

    ![Mostra as definições de Proteção de Dados no portal.](../media/account-create-2.png)

4. Em seguida, recomendamos as definições predefinidas do ecrã **Avançado** para cópia de segurança para os casos de utilização do Azure.

    ![Mostra o separador definições avançadas no portal.](../media/account-create-3.png)

5. Adicione tags para organização se utilizar a marcação e crie a sua conta.

6. Dois passos rápidos são tudo o que é necessário agora antes de poder adicionar a conta ao seu ambiente Veeam. Navegue para a conta que criou no portal Azure e selecione **Recipientes** no menu **de serviço Blob.** Adicione um recipiente e escolha um nome significativo. Em seguida, navegue para o item **das teclas de acesso** em **Definições** e copie o nome da **conta de Armazenamento** e uma das duas teclas de acesso. Você precisará do nome do recipiente, nome da conta e chave de acesso nos próximos passos.

    ![Mostra a criação de contentores no portal.](../media/container-b.png)

    ![Mostra as definições das chaves de acesso no portal.](../media/access-key.png)

    > [!Note]
    > A Reserva e Replicação do Veeam oferece opções adicionais para ligar ao Azure. Para o caso de utilização deste artigo, utilizar o Microsoft Azure Blob Storage como alvo de backup, utilizar o método acima é a melhor prática recomendada.

7. *(Opcional)* Pode adicionar mais camadas de segurança à sua implantação.

     1. Configure o acesso baseado em funções para limitar quem pode fazer alterações na sua conta de armazenamento. Para mais informações, consulte [funções incorporadas para operações de gestão.](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)

     1. Restringir o acesso à conta a segmentos de rede específicos com [definições de firewall de armazenamento](../../../../common/storage-network-security.md) para impedir tentativas de acesso fora da sua rede corporativa.

        ![Mostra as definições de firewall de armazenamento no portal.](../media/storage-firewall.png)

     1. Descoda um bloqueio de [eliminação](../../../../../azure-resource-manager/management/lock-resources.md) na conta para evitar a eliminação acidental da conta de armazenamento.

        ![Bloqueio de recursos](../media/resource-lock.png)

     1. Configure as [melhores práticas de segurança adicionais.](../../../../../storage/blobs/security-recommendations.md)

8. Na Consola de Gestão de Cópias de Segurança e Replicação do Veaam, navegue para **a Infraestrutura de Backup** -> clique à direita no painel de visão geral e selecione Add Backup **Repository** para abrir o assistente de configuração. Na caixa de diálogo, selecione **o armazenamento de**  ->  **objetos Microsoft Azure Blob Storage**  ->  **Azure Blob Storage**.

    ![Mostra a seleção do armazenamento de objetos no Veeam Repository Wizard.](../media/veeam-repo-a.png)

    ![Mostra a seleção do Microsoft Azure Blob Storage no Veeam Repository Wizard.](../media/veeam-repo-b.png)

    ![Mostra a seleção do Armazenamento Azure Blob no Feiticeiro de Repositório de Veeam.](../media/veeam-repo-c.png)

9. Em seguida, especifique um nome e uma descrição do seu novo repositório de armazenamento Blob.

    ![Mostra escrever um nome para o repositório no Feiticeiro de Repositório de Veeam.](../media/veeam-repo-d.png)

10. No passo seguinte, adicione as credenciais para aceder à sua conta de armazenamento Azure. Selecione **a Conta de Armazenamento do Microsoft Azure** no Gestor de Credenciais cloud e introduza o nome da sua conta de armazenamento e a chave de acesso. Selecione **Azure Global** no seletor da região e qualquer servidor de gateway, se aplicável.

    ![Mostra especificar uma conta no Veeam Repository Wizard.](../media/veeam-repo-e.png)

    > [!Note]
    > Se optar por não utilizar um servidor de gateway Veeam, certifique-se de que todas as extensões de repositórios de escala têm acesso direto à Internet.

11. No registo do contentor, selecione o seu recipiente de armazenamento Azure e selecione ou crie uma pasta para armazenar as suas cópias de segurança. Também pode definir um limite suave na capacidade de armazenamento global a utilizar pelo Veeam, que é recomendado. Reveja as informações apresentadas na secção de resumo e preencha a ferramenta de configuração. Pode agora selecionar o novo repositório na configuração de trabalho de reserva.

    ![Mostra especificar um recipiente no Feiticeiro de Repositório de Veeam.](../media/veeam-repo-f.png)

    ![Mostra a criação de uma pasta no Feiticeiro de Repositório de Veeam.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>Orientação operacional

### <a name="azure-alerts-and-performance-monitoring"></a>Alertas de Azure e monitorização de desempenho

É aconselhável monitorizar tanto os seus recursos Azure como a capacidade do Veeam de os alavancar como faria com qualquer alvo de armazenamento em que confia para armazenar as suas cópias de segurança. Uma combinação das capacidades de monitorização do Azure Monitor e veeam (o separador **estatísticas** no nó **Jobs** da Consola de Gestão de Veeam ou opções mais avançadas como o Veeam One Reporter) irá ajudá-lo a manter o seu ambiente saudável.

#### <a name="azure-portal"></a>Portal do Azure

O Azure fornece uma solução de monitorização robusta sob a forma de [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Pode [configurar o Azure Monitor](../../../../blobs/monitor-blob-storage.md) para monitorizar a capacidade de armazenamento do Azure, transações, disponibilidade, autenticação e muito mais. A referência completa das métricas rastreadas pode ser encontrada [aqui.](../../../../blobs/monitor-blob-storage-reference.md) Algumas métricas úteis a seguir são BlobCapacity - para se certificar de que permanece abaixo do limite máximo de [capacidade de armazenamento,](../../../../common/scalability-targets-standard-account.md)Ingress e Egress - para rastrear a quantidade de dados que estão sendo escritos e lidos a partir da sua conta de armazenamento Azure, e SuccessE2ELatency - para acompanhar o tempo de ida e volta para pedidos de e para a Azure Storage e seu MediaAgent.

Também pode [criar alertas de registo](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) para rastrear a saúde do serviço de armazenamento Azure e ver o painel de [instrumentos de estado Azure](https://status.azure.com/status) a qualquer momento.

#### <a name="veeam-reporting"></a>Relatórios de Veeam

- [Configure Veeam One Reporting](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Alarmes de backup e replicação de Veeam](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>Como abrir casos de apoio

Quando precisar de ajuda com a sua cópia de segurança para a solução Azure, deverá abrir um caso tanto com o Veeam como com o Azure. Isto ajuda as nossas organizações de apoio a colaborar, se necessário.

#### <a name="to-open-a-case-with-veeam"></a>Para abrir um caso com Veeam

No [site de apoio ao cliente da Veeam,](https://www.veeam.com/support.html)inscreva-se e abra um caso.

Para compreender as opções de suporte disponíveis pela Veeam, consulte a Política de Apoio ao Cliente do [Veeam.](https://www.veeam.com/veeam_software_support_policy_ds.pdf)

Também pode ligar para abrir um caso: [Números de suporte a nível mundial](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Para abrir um caso com Azure

No [portal Azure](https://portal.azure.com) procure **apoio** na barra de pesquisa no topo. Selecione **Ajuda + suporte** novo pedido de  ->  **suporte.**

> [!NOTE]
> Quando abrir um caso, seja específico que necessita de assistência com o Azure Storage ou com a Azure Networking. Não especifique a cópia de segurança do Azure. Azure Backup é o nome de um serviço Azure e o seu caso será encaminhado incorretamente.

### <a name="links-to-relevant-veeam-documentation"></a>Links para documentação relevante do Veeam

Consulte a seguinte documentação do Veeam para mais detalhes:

- [Guia do Utilizador veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Guia de Arquitetura de Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Ofertas de mercado

Pode continuar a utilizar a solução Veeam que conhece e confiar para proteger as suas cargas de trabalho em funcionamento no Azure. A Veeam facilitou a implementação da sua solução no Azure e protege as Máquinas Virtuais Azure e muitos outros serviços Azure.

- [Implementar cópia de segurança do Veeam & replicação através do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Site de backup e recuperação do Veeam's Azure](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes recursos no site da Veeam para obter informações sobre cenários de utilização especializados:

- [Veeam Como Vídeos](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Documentação Técnica de Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Base de Conhecimento de Veeam e FAQ](https://www.veeam.com/knowledge-base.html?ad=menu-resources)