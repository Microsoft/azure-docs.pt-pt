---
title: Ressarça os seus dados para Azure com Commvault
titleSuffix: Azure Storage
description: Fornece uma visão geral dos fatores a considerar e passos a seguir para usar o Azure como alvo de armazenamento e localização de recuperação para o Commvault Complete Backup and Recovery
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 29484bee685fcc15073ef255e65a780e05f1200d
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561673"
---
# <a name="backup-to-azure-with-commvault"></a>Backup para Azure com Commvault

Este artigo ajuda-o a integrar uma infraestrutura commvault com armazenamento Azure Blob. Inclui pré-requisitos, considerações, implementação e orientação operacional. Este artigo aborda a utilização do Azure como alvo de backup fora do local e um local de recuperação se ocorrer um desastre, o que impede o funcionamento normal dentro do seu local principal.

> [!NOTE]
> A Commvault oferece uma solução de tempo de recuperação mais baixa (RTO), Commvault Live Sync. Esta solução permite-lhe ter um VM de espera que pode ajudá-lo a recuperar mais rapidamente em caso de desastre num ambiente de produção Azure. Estas capacidades estão fora do âmbito deste documento.

## <a name="reference-architecture"></a>Arquitetura de referência

O diagrama seguinte fornece uma arquitetura de referência para as instalações de Azure e in-Azure.

![Commvault to Azure Reference Architecture](../media/commvault-diagram.png)

A sua implementação commvault existente pode facilmente integrar-se com o Azure adicionando uma conta de armazenamento Azure, ou várias contas, como alvo de armazenamento em nuvem. A Commvault também permite que você recupere backups de instalações dentro de Azure dando-lhe um site de recuperação a pedido em Azure.

## <a name="commvault-interoperability-matrix"></a>Matriz de interoperabilidade commvault

| Carga de trabalho | Armazenamento de GPv2 e Blob | Suporte de nível legal | Suporte de nível de arquivo | Suporte familiar data box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| VMs/dados no local | v11.5 | v11.5 | v11.10 | v11.10 |
| VMs do Azure | v11.5 | v11.5 | v11.5 | N/D |
| Blob do Azure | v11.6 | v11.6 | v11.6 | N/D |
| Ficheiros do Azure | v11.6 | v11.6 | v11.6 | N/D |

## <a name="before-you-begin"></a>Antes de começar

Um pequeno planeamento inicial irá ajudá-lo a usar o Azure como alvo de backup e local de recuperação.

### <a name="get-started-with-azure"></a>Introdução ao Azure

A Microsoft oferece uma estrutura a seguir para começar com o Azure. O [Cloud Adoption Framework](https://docs.microsoft.com/azure/architecture/cloud-adoption/) (CAF) é uma abordagem detalhada da transformação digital empresarial e guia abrangente para planear uma adoção em nuvem de nível de produção. O CAF inclui um guia de configuração passo a passo [Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) para ajudá-lo a levantar-se e a correr de forma rápida e segura. Pode encontrar uma versão interativa no [portal Azure.](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade) Você encontrará arquiteturas de amostra, boas práticas específicas para implementar aplicações, e recursos de formação gratuitos para colocá-lo no caminho para a experiência Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Considere a rede entre a sua localização e a Azure

Seja utilizando recursos em nuvem para executar a produção, teste e desenvolvimento, ou como um site de backup e recuperação, é importante entender as suas necessidades de largura de banda para sementeira inicial de backup e para transferências diárias em curso.

A Azure Data Box fornece uma forma de transferir a sua linha de base inicial de backup para Azure sem precisar de mais largura de banda. Isto é útil se a transferência de base for estimada para demorar mais tempo do que você pode tolerar. Pode utilizar o estimador de Transferência de Dados quando criar uma conta de armazenamento para estimar o tempo necessário para transferir a sua cópia de segurança inicial.

![Mostra o estimador de transferência de dados do Azure Storage no portal.](../media/az-storage-transfer.png)

Lembre-se, você precisará de capacidade de rede suficiente para suportar transferências diárias de dados dentro da janela de transferência necessária (janela de backup) sem afetar aplicações de produção. Esta secção descreve as ferramentas e técnicas disponíveis para avaliar as necessidades da sua rede.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Determine a largura de banda que vai precisar

Para determinar a largura de banda que precisa, use os seguintes recursos:

- Relatórios do seu software de reserva.
- A Commvault fornece relatórios padrão para determinar a [taxa de variação](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) e o tamanho total do [conjunto de backup](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) para a transferência inicial da linha de base para o Azure.
- Ferramentas de avaliação e relatório independentes de software de backup como:
  - [Rio MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
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
|**# de regiões**     | 1         | 1         | 2         | 2 |
|**Falha manual na região secundária**     | N/D         | N/D         | Sim         | Sim |

**Camadas de armazenamento de bolhas:**

|  | Nível quente   |Nível fresco   | Nível de arquivo |
| ----------- | ----------- | -----------  | -----------  |
| **Disponibilidade** | 99,9%         | 99%         | Offline      |
| **Taxas de utilização** | Custos de armazenamento mais elevados, menor acesso e custos de transação | Custos de armazenamento mais baixos, acesso mais elevado e custos de transação | Custos de armazenamento mais baixos, acesso mais elevado e custos de transação |
| **Retenção de dados mínimos necessária**| N/D | 30 dias | 180 dias |
| **Latência (tempo para primeiro byte)** | Milissegundos | Milissegundos | Horas |

#### <a name="sample-backup-to-azure-cost-model"></a>Cópia de apoio ao modelo de custos da Azure

Com o pay-per-use pode ser assustador para os clientes que são novos na nuvem. Enquanto paga apenas pela capacidade utilizada, também paga por transações (ler e escrever) e [saída para os dados lidos](https://azure.microsoft.com/pricing/details/bandwidth/) no seu ambiente no local quando a [Rota Expresso Azure direta local ou Rota Expresso plano de dados ilimitados](https://azure.microsoft.com/pricing/details/expressroute/) estão em uso onde a saída de dados do Azure está incluída. Pode utilizar a [Calculadora de Preços Azure](https://azure.microsoft.com/pricing/calculator/) para realizar a análise "e se". Pode basear a análise nos preços da lista ou nos [preços de capacidade reservada de armazenamento Azure,](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)que podem proporcionar até 38% de poupança. Aqui está um exercício de preços de exemplo para modelar o custo mensal de apoio ao Azure. Este é apenas um exemplo. *O seu preço pode variar devido a atividades não capturadas aqui.*

|Fator de custo  |Custo mensal  |
|---------|---------|
|100 TB de dados de backup no armazenamento fresco     |$1556.48         |
|2 TB de novos dados escritos por dia x 30 Dias     |$39 em transações          |
|Total estimado mensal     |$1595.48         |
|---------|---------|
|Uma vez restabelecendo 5 TB para as instalações através da internet pública   | $491.26         |

> [!NOTE]
> Esta estimativa foi gerada na Calculadora de Preços Azure utilizando preços de Pagamento do Leste dos EUA e baseia-se no padrão commvault de 32 MB tamanho sub-chunk que gera 65.536 pedidos PUT (write transactions), por dia. Este exemplo pode não ser aplicável às suas necessidades.

## <a name="implementation-guidance"></a>Orientação de implementação

Esta secção fornece um breve guia para como adicionar O Armazenamento Azure a uma implantação commvault no local. Para obter considerações detalhadas sobre orientação e planeamento, consulte o [guia de arquitetura de nuvem pública commvault para o Microsoft Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Abra o portal Azure e procure **contas de armazenamento.** Também pode clicar no ícone de **contas de armazenamento** predefinido.

    ![Mostra a adição de uma conta de armazenamento no portal Azure.](../media/azure-portal.png)
  
    ![Mostra onde escreveu armazenamento na caixa de pesquisa do portal Azure.](../media/locate-storage-account.png)

2. Selecione **Criar** para adicionar uma conta. Selecione ou crie um grupo de recursos, forneça um nome único, escolha a região, selecione desempenho **standard,** deixe sempre o tipo de conta como **Storage V2,** escolha o nível de replicação que vai ao encontro dos seus SLAs, e o nível padrão o seu software de backup será aplicado. Uma conta de Armazenamento Azure disponibiliza níveis quentes, frescos e de arquivo numa única conta e as políticas commvault permitem-lhe utilizar vários níveis para gerir eficazmente o ciclo de vida dos seus dados.

    ![Mostra as definições da conta de armazenamento no portal](../media/account-create-1.png)

3. Mantenha as opções de rede padrão por enquanto e passe para a **proteção de dados**. Aqui, pode optar por permitir a eliminação suave, o que lhe permite recuperar um ficheiro de backup acidentalmente eliminado dentro do período de retenção definido e oferece proteção contra a eliminação acidental ou maliciosa.

    ![Mostra as definições de Proteção de Dados no portal.](../media/account-create-2.png)

4. Em seguida, recomendamos as definições predefinidas do ecrã **Avançado** para cópia de segurança para os casos de utilização do Azure.

    ![Mostra o separador definições avançadas no portal.](../media/account-create-3.png)

5. Adicione tags para organização se utilizar a marcação e crie a sua conta.

6. Dois passos rápidos são tudo o que é necessário agora antes de poder adicionar a conta ao seu ambiente commvault. Navegue para a conta que criou no portal Azure e selecione **Recipientes** no menu **de serviço Blob.** Adicione um recipiente e escolha um nome significativo. Em seguida, navegue para o item **das teclas de acesso** em **Definições** e copie o nome da **conta de Armazenamento** e uma das duas teclas de acesso. Vai precisar do nome do contentor, nome da conta e chave de acesso nos próximos passos.

    ![Mostra a criação de contentores no portal.](../media/container.png)

    ![Mostra as definições das chaves de acesso no portal.](../media/access-key.png)

7. (*Opcional)* Pode adicionar camadas adicionais de segurança à sua implantação.

    1. Configure o acesso baseado em funções para limitar quem pode fazer alterações na sua conta de armazenamento. Para mais informações, consulte [funções incorporadas para operações de gestão.](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)
    1. Restringir o acesso à conta a segmentos de rede específicos com [definições de firewall de armazenamento](../../../../common/storage-network-security.md) para impedir tentativas de acesso fora da sua rede corporativa.

        ![Mostra as definições de firewall de armazenamento no portal.](../media/storage-firewall.png)

    1. Descoda um bloqueio de [eliminação](../../../../../azure-resource-manager/management/lock-resources.md) na conta para evitar a eliminação acidental da conta de armazenamento.

        ![Mostra a definição de uma fechadura de exclusão no portal.](../media/resource-lock.png)

    1. Configure as [melhores práticas de segurança adicionais.](../../../../../storage/blobs/security-recommendations.md)

1. No Centro de Comando commvault, navegue para **Gerir** o Gestor  ->    ->  **credencial de** Segurança . Escolha uma **Conta Cloud,** **tipo fornecedor** do **Microsoft Azure Storage**, selecione o **MediaAgent**, que transferirá dados de e para a Azure, adicione o nome da conta de armazenamento e a chave de acesso.

    ![Mostra a adição de credenciais no Centro de Comando commvault.](../media/commvault-credential.png)

9. Em seguida, navegue para a Nuvem **de Armazenamento** no Centro de Comando  ->   commvault. Escolha **adicionar**. Introduza um nome amigável para a conta de armazenamento e, em seguida, selecione **o Microsoft Azure Storage** da lista **Tipo.** Selecione um servidor media agent para ser usado para transferir cópias de segurança para o Azure Storage. Adicione o recipiente que criou, escolha o nível de armazenamento para utilizar dentro da conta de armazenamento Azure e selecione as credenciais criadas no #8 Passo. Por fim, escolha transferir ou não cópias de segurança deduplicadas e uma localização para a base de dados deduplica.

     ![Screenshot da interface de utilizador de nuvem Add da Commvault. No menu suspenso do Arquivo, é selecionado **Archive**](../media/commvault-add-storage.png)

10. Por fim, adicione o seu novo recurso de Armazenamento Azure a um plano existente ou novo no Centro de Comando Commvault via **Manage**  ->  **Plans** como destino de backup.

    ![Screenshot da interface de utilizador do Centro de Comando commvault. Na navegação à esquerda, em **Manage**, **Plans** é selecionado.](../media/commvault-plan.png)

11. *(Opcional)* Se planeia usar o Azure como um site de recuperação ou commvault para migrar servidores e aplicações para Azure, é uma boa prática implementar um Proxy VSA em Azure. Pode encontrar instruções detalhadas [aqui.](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)

## <a name="operational-guidance"></a>Orientação operacional

### <a name="azure-alerts-and-performance-monitoring"></a>Alertas de Azure e monitorização de desempenho

É aconselhável monitorizar tanto os seus recursos Azure como a capacidade da Commvault de os alavancar como faria com qualquer alvo de armazenamento em que confia para armazenar as suas cópias de segurança. Uma combinação de monitorização do Azure Monitor e do Centro de Comandos Commvault irá ajudá-lo a manter o seu ambiente saudável.

#### <a name="azure-portal"></a>Portal do Azure

O Azure fornece uma solução de monitorização robusta sob a forma de [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Pode [configurar o Azure Monitor](../../../../common/monitor-storage.md) para monitorizar a capacidade de armazenamento do Azure, transações, disponibilidade, autenticação e muito mais. Pode encontrar a referência completa das métricas que são recolhidas [aqui.](../../../../blobs/monitor-blob-storage-reference.md) Algumas métricas úteis a seguir são BlobCapacity - para se certificar de que permanece abaixo do limite máximo de capacidade de [armazenamento,](../../../../common/scalability-targets-standard-account.md)Ingress e Egress - para rastrear a quantidade de dados que estão sendo escritos e lidos a partir da sua conta de Armazenamento Azure, e SuccessE2ELatency - para acompanhar o tempo de ida e volta para pedidos de e para a Azure Storage e seu MediaAgent.

Também pode [criar alertas de registo](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) para rastrear a saúde do serviço de armazenamento Azure e ver o painel de [instrumentos de estado Azure](https://status.azure.com/status) a qualquer momento.

#### <a name="commvault-command-center"></a>Centro de Comando Commvault

- [Crie um alerta para piscinas de armazenamento em nuvem](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Para obter informações sobre onde pode ver relatórios de desempenho, conclusão de trabalho e iniciar a resolução básica de [problemas,](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm)consulte Dashboards .

### <a name="how-to-open-support-cases"></a>Como abrir casos de apoio

Quando precisar de ajuda com a sua cópia de segurança para a solução Azure, deverá abrir um caso tanto com a Commvault como com a Azure. Isto ajuda as nossas organizações de apoio a colaborar, se necessário.

#### <a name="to-open-a-case-with-commvault"></a>Para abrir um caso com Commvault

No [Site de Apoio ao Commvault,](https://www.commvault.com/support)inscreva-se e abra um caso.

Para compreender as opções de contrato de apoio disponíveis para si, consulte [as opções de suporte da Commvault](https://ma.commvault.com/support)

Também pode ligar para abrir um caso ou contactar o Commvault Support por e-mail:

- Portagem grátis: +1 877-780-3077
- [Números de suporte mundial](https://ma.commvault.com/Support/TelephoneSupport)
- [Suporte de e-mail Commvault](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Para abrir um caso com Azure

No [portal Azure](https://portal.azure.com) procure **apoio** na barra de pesquisa no topo. Selecione **Ajuda + suporte** novo pedido de  ->  **suporte.**

> [!NOTE]
> Quando abrir um caso, seja específico que necessita de assistência com o Azure Storage ou com a Azure Networking. Não especifique a cópia de segurança do Azure. Azure Backup é o nome de um serviço Azure e o seu caso será encaminhado incorretamente.

### <a name="links-to-relevant-commvault-documentation"></a>Links para documentação commvault relevante

Consulte a seguinte documentação commvault para mais detalhes:

- [Guia do Utilizador Commvault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Guia de Arquitetura Commvault Azure](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16)

### <a name="marketplace-offerings"></a>Ofertas de mercado

A Commvault facilitou a implementação da sua solução em Azure para proteger as Máquinas Virtuais Azure e muitos outros serviços Azure. Para obter mais informações, veja as seguintes referências:

- [Implementar Commvault através do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Commvault para a folha de dados do Azure](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Commvault lista de funcionalidades e serviços suportados do Azure](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Como usar o Commvault para proteger o SAP HANA em Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Passos seguintes

Consulte estes recursos commvault adicionais para obter informações sobre cenários de utilização especializados.

- [Use commvault para migrar os seus servidores e aplicações para a Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Proteja o SAP em Azure com o Commvault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Proteger Office365 com Commvault](https://www.youtube.com/watch?v=dl3nvAacxZU)
