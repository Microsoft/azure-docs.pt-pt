---
title: Ressarça os seus dados para Azure com Commvault
titleSuffix: Azure Blob Storage Docs
description: A página web fornece uma visão geral dos fatores a considerar e passos a seguir para alavancar o Azure como alvo de armazenamento e localização de recuperação para o Commvault Complete Backup and Recovery
keywords: Commvault, Backup to Cloud, Backup, Backup to Azure, Recuperação de Desastres, Continuidade do Negócio
author: Karl Rautenstrauch
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: Storage
ms.subservice: Blob Storage
ms.openlocfilehash: f5b35abd58d99478014c1227b6e3c03c2fc7a177
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746200"
---
# <a name="back-up-to-azure-with-commvault"></a>De volta a Azure com Commvault

Este artigo fornece um guia para a integração de uma infraestrutura commvault com o Azure Blob Storage. Inclui pré-requisitos, princípios de armazenamento Azure, implementação e orientação operacional. Este artigo apenas aborda a utilização do Azure como alvo de backup fora do local e um local de recuperação em caso de desastre, o que impede o funcionamento normal dentro do seu local principal. A Commvault também oferece uma solução de RTO mais baixa, a Commvault Live Sync, como forma de ter um VM de espera pronto para arrancar e recuperar mais rapidamente em caso de desastre e proteção de recursos dentro de um ambiente de Produção Azure. Estas capacidades estão fora de alcance para este documento. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Arquitetura de referência para instalações no local para implantações de Azure e In-Azure

![Commvault to Azure Reference Architecture](../media/commvault-diagram.png)

A sua implementação commvault existente pode facilmente integrar-se com o Azure adicionando uma Conta de Armazenamento Azure, ou várias contas, como alvo de Armazenamento em Nuvem. A Commvault também permite que você recupere backups de instalações dentro de Azure dando-lhe um site de recuperação a pedido em Azure.   

## <a name="commvault-interoperability-matrix"></a>Matriz de interoperabilidade commvault
| Carga de trabalho | Armazenamento de GPv2 e Blob | Suporte cool tier | Suporte de Nível de Arquivo | Suporte familiar da caixa de dados |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| VMs/dados no local | v11.5 | v11.5 | v11.10 | v11.10 |
| VMs do Azure | v11.5 | v11.5 | v11.5 | ND |
| Blob do Azure | v11.6 | v11.6 | v11.6 | ND |
| Ficheiros do Azure | v11.6 | v11.6 | v11.6 | ND | 

## <a name="before-you-begin"></a>Antes de começar

Um pequeno planeamento inicial irá garantir que você se junte às fileiras de muitos, muitos clientes felizes usando Azure como um alvo de backup fora do local e site de recuperação.

### <a name="are-you-new-to-azure"></a>É novo em Azure?

A Microsoft oferece uma estrutura a seguir para começar com o Azure. O [Cloud Adopt Framework](https://docs.microsoft.com/azure/architecture/cloud-adoption/) CAF é uma \( \) abordagem detalhada da transformação digital empresarial e guia abrangente para o planeamento de uma adoção em nuvem de grau de produção. O CAF inclui um Guia de [Configuração Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) passo a passo para os novos azure para ajudá-lo a levantar-se e a correr de forma rápida e segura e pode encontrar uma versão interativa no [Portal Azure.](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade) Você encontrará arquiteturas de amostra e boas práticas específicas para implantar aplicações e recursos de formação gratuitos para colocá-lo no caminho para a experiência Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Considere a rede entre a sua localização e a Azure

Quer alavancar os recursos da Cloud para executar a Produção, Teste e Desenvolvimento, ou como um alvo de backup e site de recuperação, é importante entender as suas necessidades de largura de banda para sementeira inicial de backup e para transferências diárias em curso. 

A Azure Data Box fornece um meio para transferir a sua linha de base inicial de backup para Azure sem precisar de largura de banda adicional se a transferência de base for estimada para demorar mais tempo do que pode tolerar. Pode aproveitar o estimador de Transferência de Dados quando criar uma conta de armazenamento para estimar o tempo necessário para transferir a sua cópia de segurança inicial.

![Estimativa de transferência de dados de armazenamento de Azure](../media/az-storage-transfer.png)

Lembre-se, você precisará de capacidade de rede suficiente para suportar transferências diárias de dados dentro da janela de transferência necessária (janela de reserva) sem afetar aplicações de produção. Esta secção irá delinear as ferramentas e técnicas disponíveis para avaliar as necessidades da sua rede.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Como pode determinar a largura de banda que vai precisar?

-  Relatórios do seu software de reserva. 
  A Commvault fornece relatórios padrão para determinar a [taxa de variação](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) e o tamanho total do [conjunto de backup](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) para a transferência inicial da linha de base para o Azure.
- Ferramentas de avaliação e relatório independentes de software de backup como:
  - [Rio MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="how-will-i-know-how-much-headroom-i-have-with-my-current-internet-connection"></a>Como vou saber quanto espaço tenho com a minha ligação à Internet atual?

É importante saber quanto espaço de cabeça, ou tipicamente não utilizado, largura de banda você tem disponível no dia-a-dia. Isto permitir-lhe-á avaliar adequadamente se consegue cumprir os seus objetivos durante o tempo inicial de carregamento, quando não utilizar a Caixa de Dados Azure para sementeira offline, e para completar backups diários com base na taxa de alteração identificada acima e na sua janela de backup. Abaixo estão os métodos que pode usar para identificar a sala de entrada de largura de banda que os seus backups para Azure são livres de consumir.

- É um cliente Azure ExpressRoute existente? Veja a [utilização](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts#circuits-metrics) do circuito no portal Azure.
- Pode contactar o seu ISP. Devem ter relatórios para partilhar consigo ilustrando a sua utilização diária e mensal existente.
- Existem várias ferramentas que podem medir a utilização monitorizando o tráfego da sua rede ao nível do seu router/switch, incluindo:
  - [Pacote de analisador de largura de banda solarwinds](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Assistente de Rede Cisco](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choosing-the-right-storage-options"></a>Escolher as opções de armazenamento certas

Ao usar o Azure como alvo de backup, os clientes fazem uso do [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)\. O armazenamento Azure Blob é a solução de armazenamento de objetos da Microsoft. O armazenamento de blob é otimizado para armazenar quantidades massivas de dados não estruturados, que são dados que não aderem a nenhum modelo ou definição de dados. Além disso, o Azure Storage é durável, altamente disponível, seguro e escalável. A plataforma da Microsoft oferece flexibilidade para selecionar o armazenamento certo para a carga de trabalho certa, de modo a fornecer o [nível de resiliência](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json) para atender os seus SLAs internos. Blob Storage é um serviço pay-per-use. É [cobrado mensalmente](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal#pricing-and-billing) pela quantidade de dados armazenados, acedendo a esses dados, e - no caso de Cool and Archive Tiers - um período mínimo de retenção exigido. As opções de resiliência e tiering aplicáveis aos dados de backup são resumidas nas tabelas abaixo.

**Opções de resiliência de armazenamento Azure Blob:**

|  |Localmente Redundante  |Zona Redundante  |Geograficamente redundante  |Zona Geo Redundante  |
|---------|---------|---------|---------|---------|
|Eficaz # de Cópias     | 3         | 3         | 6         | 6 |
|# de Zonas de Disponibilidade     | 1         | 3         | 2         | 4 |
|# de Regiões     | 1         | 1         | 2         | 2 |
|Falha manual na Região Secundária     | ND         | ND         | Sim         | Sim |

**Níveis de armazenamento Azure Blob:**

|  | Nível quente   |Nível Cool   | Nível de arquivo |
| ----------- | ----------- | -----------  | -----------  |
| Disponibilidade | 99,9%         | 99%         | Offline      |
| Custos de Utilização | Custos de armazenamento mais elevados, menor acesso e custos de transação | Custos de armazenamento mais baixos, acesso mais elevado e custos de transação | Custos de armazenamento mais baixos, acesso mais elevado e custos de transação |
| Retenção mínima de dados necessária | ND | 30 dias | 180 dias |
| Latência (Tempo para Primeiro Byte) | Milissegundos | Milissegundos | Horas |

#### <a name="sample-backup-to-azure-cost-model"></a>Amostra de backup para modelo de custos Azure

O conceito de pay-per-use pode ser assustador para os clientes que são novos na Nuvem Pública. Enquanto paga apenas pela capacidade utilizada, também paga por transações (ler e escrever) e [saída para os dados lidos](https://azure.microsoft.com/pricing/details/bandwidth/) no seu ambiente no local quando o [plano de dados diretos da Rota Expresso Azure ou a Rota Expresso De Dados Ilimitados](https://azure.microsoft.com/pricing/details/expressroute/) estão em uso onde a saída de dados do Azure está incluída. Pode efetuar o que se a análise for feita com base nos preços das listas ou com [o preço reservado de capacidade do Azure Storage,](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)que pode proporcionar até 38% de poupança, na [Calculadora de Preços Azure](https://azure.microsoft.com/pricing/calculator/). Aqui está um exercício de preços de exemplo para modelar o custo mensal de backup até Azure, este é apenas um exemplo e ***o seu preço pode variar devido a atividades não capturadas aqui:***


|Fator de Custo  |Custo Mensal  |
|---------|---------|
|100 TB de dados de backup no armazenamento legal     |$1556.48         |
|2 TB de novos dados escritos por dia x 30 Dias     |$39 em transações          |
|Total mensal estimado     |$1595.48         |
|---------|---------|
|Uma vez restaurar 5 TB para as instalações através da Internet Pública   | $491.26         |


> [!Note] 
Esta estimativa foi gerada na Calculadora de Preços Azure utilizando os preços do East US Pay-as-you-go e baseia-se no padrão commvault de tamanho sub-chunk de 32MB que gera 65.536 put requests, aka write transactions, por dia. Este exemplo pode não ser aplicável às suas necessidades.

## <a name="implementation-and-operational-guidance"></a>Implementação e orientação operacional

Esta secção fornece um breve guia para adicionar O Armazenamento Azure a uma implantação commvault no local. Se estiver interessado em orientações detalhadas e considerações de planeamento, recomendamos a revisão do [Guia de Arquitetura Commvault Azure.](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16)

1. Abra o portal Azure e procure por "Contas de Armazenamento" ou clique no ícone de serviços predefinidos.
    
    1. ![Portal do Azure](../media/azure-portal.png)
  
    1. ![Contas de armazenamento no Portal Azure](../media/locate-storage-account.png)

2. Escolha adicionar uma conta, e selecione ou crie um Grupo de Recursos, forneça um nome único, escolha a região, selecione "Standard" Performance, deixe sempre o tipo de conta como "Storage V2", escolha o nível de replicação, que vai ao encontro dos seus SLAs, e o nível padrão que o seu software de backup irá alavancar. Uma conta de Armazenamento Azure disponibiliza os níveis hot, cool e Archive numa única conta e as políticas commvault permitem-lhe alavancar vários níveis para gerir eficazmente o ciclo de vida dos seus dados. Avance para o passo seguinte. 

    ![Criação de uma conta de armazenamento](../media/account-create-1.png)

3. Mantenha-se com as opções de rede padrão por enquanto e passe para "Proteção de Dados". Aqui, pode optar por ativar o "Soft Delete" que lhe permite recuperar um ficheiro de Backup acidentalmente eliminado dentro do período de retenção definido e oferece proteção contra a eliminação acidental ou maliciosa. 
    
    ![Criação de uma conta de armazenamento Parte 2](../media/account-create-2.png)

4. Em seguida, recomendamos as definições predefinidas do ecrã "Advanced" para os casos de utilização de Backup para Azure.

    ![Criação de uma conta de armazenamento Parte 3](../media/account-create-3.png) 

5. Adicione tags para organização se alavancar a marcação e criar a sua conta. Tem agora petabytes de armazenamento a pedido à sua disposição!

6. Dois passos rápidos são tudo o que é necessário agora antes de poder adicionar a conta ao seu ambiente commvault. Navegue para a conta que criou no portal Azure e selecione "Contentores" no menu "Blob Service" na lâmina Portal. Adicione um novo recipiente e escolha um nome significativo. Em seguida, navegue para o item "Teclas de acesso" em "Definições" e copie o "nome da conta de armazenamento" e uma das duas teclas de acesso. Você precisará do nome do Recipiente, Nome da Conta e Chave de Acesso nos nossos próximos passos.
    
    ![Criação de um Recipiente](../media/container.png)
    
    ![Pegue a informação da conta](../media/access-key.png)

7. ***(Opcional)*** Pode adicionar camadas adicionais de segurança à sua implantação.
    
    1. Configure o acesso baseado em funções para limitar quem pode escoar alterações na sua Conta de Armazenamento. [Saiba mais aqui](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)
    1. Restringir o acesso à conta a segmentos de rede específicos com [firewall de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) para evitar tentativas de acesso fora da sua rede corporativa.

    ![Firewall de armazenamento](../media/storage-firewall.png) 

    1. Descoda um [bloqueio de exclusão](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) na conta para evitar a eliminação acidental da Conta de Armazenamento.

    ![Bloqueio de recursos](../media/resource-lock.png)
    
    1. Configure as [melhores práticas de segurança adicionais.](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)
    
1. No Centro de Comando commvault, navegue para "Gerir" -- > "Segurança" - > "Gerente Credencial". Escolha uma "Conta Cloud", "Tipo de Fornecedor" do Microsoft Azure Storage, selecione o "MediaAgent", que transferirá dados de e para o Azure, adicione o Nome da Conta de Armazenamento e a Chave de Acesso.
    
    ![Credencial commvault](../media/commvault-credential.png)

9. Em seguida, navegue para "Storage" --> "Cloud" no Centro de Comando commvault. Escolha "Adicionar". Introduza um nome amigável para a Conta de Armazenamento e, em seguida, selecione "Microsoft Azure Storage" da lista "Tipo". Selecione um servidor media agent para ser usado para transferir cópias de segurança para o Azure Storage. Adicione o recipiente que criou, escolha o Nível de Armazenamento para alavancar dentro da conta de Armazenamento Azure e selecione as Credenciais criadas no #8 Passo. Por fim, escolha transferir ou não cópias de segurança deduplicadas e uma localização para a base de dados deduplica.
    
     ![Commvault Adicionar Armazenamento](../media/commvault-add-storage.png)

10. Por fim, adicione o seu novo recurso de Armazenamento Azure a um plano existente ou novo no Centro de Comando commvault via "Gerir" --> "Plans" como "Destino de Reserva".

    ![Commvault Adicionar Armazenamento](../media/commvault-plan.png)

11. ***(Opcional)*** Se planeia aproveitar o Azure como um site de recuperação ou commvault para migrar servidores e aplicações para Azure, é uma boa prática implementar um Proxy VSA em Azure. Pode encontrar instruções detalhadas [aqui.](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)  

### <a name="azure-alerting-and-performance-monitoring"></a>Alerta de Azure e monitorização do desempenho

É aconselhável monitorizar tanto os seus recursos Azure como a capacidade da Commvault de os alavancar como faria com qualquer alvo de armazenamento em que confia para armazenar as suas cópias de segurança. Uma combinação de monitorização do Azure Monitor e do Centro de Comandos Commvault irá ajudá-lo a manter o seu ambiente saudável.

#### <a name="microsoft-azure-portal"></a>Portal do Microsoft Azure

O Microsoft Azure fornece uma solução de monitorização robusta sob a forma de [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). Pode [configurar o Azure Monitor](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) para monitorizar a capacidade de armazenamento do Azure, transações, disponibilidade, autenticação e muito mais. A referência completa das métricas rastreadas pode ser encontrada [aqui.](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference) Algumas métricas úteis a seguir são BlobCapacity - para se certificar de que permanece abaixo do limite máximo [de capacidade de armazenamento,](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account)Ingress e Egress - para rastrear a quantidade de dados que estão sendo escritos e lidos a partir da sua conta de Armazenamento Azure, e SuccessE2ELatency - para acompanhar o tempo de ida e volta para pedidos de e para a Azure Storage e seu MediaAgent. 

Também pode [criar alertas de registo](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) para rastrear a saúde do serviço de armazenamento Azure e ver o Painel de Estado do [Azure](https://status.azure.com/status) a qualquer momento.

#### <a name="commvault-command-center"></a>Centro de Comando Commvault

[Criação de alertas para piscinas de armazenamento em nuvem](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)

[Onde os clientes podem ir ver relatórios de desempenho, conclusão de trabalho e começar a resolução básica de problemas](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm) 

### <a name="how-to-open-support-cases"></a>Como abrir casos de apoio

Quando precisar de assistência com a sua Backup to Azure Solution, recomendamos que abra um caso com a Commvault e a Azure para que as nossas organizações de apoio possam participar colaborativamente, se necessário.

#### <a name="how-to-open-a-case-with-commvault"></a>Como abrir um caso com Commvault

Navegue no Site de [Suporte do Commvault,](https://www.commvault.com/support)inscreva-se e abra um caso.

Se precisar de compreender as opções de contrato de apoio disponíveis, consulte [opções de suporte commvault](https://ma.commvault.com/support)

Também pode ligar para abrir um caso ou contactar o Commvault Support por e-mail:

Portagem Grátis: +1 877-780-3077

[Números de suporte mundial](https://ma.commvault.com/Support/TelephoneSupport)

[Suporte de e-mail Commvault](mailto:support@commvault.com)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Como abrir um caso com a equipa de apoio do Azure

Dentro do [portal Azure](https://portal.azure.com) procure "Suporte" na Barra de Pesquisa no topo do portal e escolha "+ Novo Pedido de Suporte" 
> [!Note]
Ao abrir um caso, seja específico que precisa de assistência com "Azure Storage" ou "Azure Networking" e **NÃO** "Azure Backup". O Azure Backup é um serviço nativo da Microsoft Azure e o seu caso será encaminhado incorretamente.

### <a name="links-to-relevant-commvault-documentation"></a>Links para documentação commvault relevante

Documentação commvault que fornece mais detalhes:

[Guia do Utilizador Commvault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)

[Guia de Arquitetura Commvault Azure](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16) 

### <a name="link-to-marketplace-offering"></a>Link para oferta de Marketplace

Também pode continuar a utilizar a solução Commvault que conhece e em confiança para proteger as suas cargas de trabalho em funcionamento no Azure. A Commvault facilitou a implementação da sua solução no Azure e protege as Máquinas Virtuais Azure e muitos outros Serviços Azure.

[Implementar Commvault através do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)

[Ficha técnica do Azure](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)

[Lista completa de funcionalidades e serviços do Azure suportados](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)

[Como usar o Commvault para proteger o SAP HANA em Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Passos seguintes

Explore recursos adicionais nestes sites externos para obter informações sobre cenários de utilização especializados:

[Use commvault para migrar os seus servidores e aplicações para a Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)

[Proteja o SAP em Azure com o Commvault](https://www.youtube.com/watch?v=4ZGGE53mGVI)

[Proteger Office365 com Commvault](https://www.youtube.com/watch?v=dl3nvAacxZU)
