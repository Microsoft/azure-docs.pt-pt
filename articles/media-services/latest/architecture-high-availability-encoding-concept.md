---
title: Alta Disponibilidade com vídeo de serviços de mídia a pedido
description: Este artigo é uma visão geral dos serviços Azure que você pode usar para facilitar a alta disponibilidade para a aplicação VOD.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: conceptual
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a87525248273db38e4e7bc8d1b59bbd9f99bb4c6
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106995"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>Alta Disponibilidade com Serviços de Mídia e Vídeo a Pedido (VOD)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="high-availability-for-vod"></a>Alta disponibilidade para VOD

Há um padrão de design de alta disponibilidade chamado [Geodes](/azure/architecture/patterns/geodes) na documentação da Arquitetura Azure. Descreve como os recursos duplicados são mobilizados para diferentes regiões geográficas para proporcionar escalabilidade e resiliência.  Você pode usar os serviços Azure para criar tal arquitetura para cobrir muitas considerações de design de alta disponibilidade, tais como redundância, monitorização de saúde, equilíbrio de carga, e backup de dados e recuperação.  Uma dessas arquiteturas é descrita abaixo com detalhes sobre cada serviço utilizado na solução, bem como como os serviços individuais podem ser usados para criar uma arquitetura de alta disponibilidade para a sua aplicação VOD.

### <a name="sample"></a>Sample

Existe uma amostra disponível para você usar para se familiarizar com alta disponibilidade com Serviços de Mídia e Vídeo a Pedido (VOD). Também entra em mais detalhes sobre como os serviços são usados para um cenário VOD.  A amostra não se destina a ser utilizada na produção na sua forma atual.  Reveja cuidadosamente o código de amostra e o readme, particularmente a secção sobre [modos de falha](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming) antes de integrá-lo numa aplicação de produção.  Uma implementação de produção de elevada disponibilidade para vídeo a pedido (VOD) também deve rever cuidadosamente a sua estratégia de Rede de Entrega de Conteúdos (CDN).  Confira o [código no GitHub.](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)

## <a name="overview-of-services"></a>Visão geral dos serviços

Os serviços utilizados neste exemplo de arquitetura incluem:

| Ícone | Nome | Descrição |
| :--: | ---- | ----------- |
|![Este é o ícone da conta dos serviços de mídia.](./media/architecture-high-availability-encoding-concept/azure-media-services.svg)| Conta de serviços de mídia | **Description:**<br>Uma conta de Media Services é o ponto de partida para gerir, encriptar, codificar, analisar e transmitir conteúdos de mídia em Azure. Está associado a um recurso de conta Azure Storage. A conta e todo o armazenamento associado devem estar na mesma subscrição do Azure.<br><br>**Utilização do VOD:**<br>Estes são os serviços que utiliza para codificar e entregar os seus ativos de vídeo e áudio.  Para uma elevada disponibilidade, criaria pelo menos duas contas de Media Services, cada uma numa região diferente. [Saiba mais sobre a Azure Media Services](media-services-overview.md). |
|![Este é o ícone da conta de armazenamento.](./media/architecture-high-availability-encoding-concept/storage-account.svg)| Conta de armazenamento | **Description:**<br>Uma conta de armazenamento Azure contém todos os seus objetos de dados de armazenamento Azure: bolhas, ficheiros, filas, tabelas e discos. Os dados estão acessíveis em qualquer parte do mundo em HTTP ou HTTPS.<br><br>Cada conta dos Serviços de Comunicação Social, em cada região teria uma conta de armazenamento na mesma região.<br><br>**Utilização do VOD:**<br>Pode armazenar dados de entrada e saída para o processamento e streaming de VOD. [Saiba mais sobre o Azure Storage](../../storage/common/storage-introduction.md). |
|![Este é o ícone da fila de armazenamento Azure.](./media/architecture-high-availability-encoding-concept/storage-account-queue.svg)| Fila de Armazenamento do Azure | **Description:**<br>O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS.<br><br>**Utilização do VOD:**<br>As filas podem ser usadas para enviar e receber mensagens para coordenar atividades entre diferentes módulos. A amostra utiliza uma fila de armazenamento Azure, mas a Azure fornece outros tipos de filas, como o Service Bus e o Service Fabric Reliable Queues, que podem melhor se adequar às suas necessidades. [Saiba mais sobre a Fila Azure.](../../storage/queues/storage-queues-introduction.md) |
|![Este é o ícone DB Azure Cosmos.](./media/architecture-high-availability-encoding-concept/azure-cosmos-db.svg)| Azure Cosmos DB  | **Description:**<br>Azure Cosmos DB é o serviço de base de dados multi-modelo da Microsoft, distribuído globalmente, que escala de forma independente a produção e armazenamento em várias regiões do Azure em todo o mundo.<br><br>**Utilização do VOD:**<br>As tabelas podem ser usadas para armazenar registos de estado de produção de emprego e para acompanhar o estado de saúde de cada instância dos Serviços de Comunicação Social. Também pode rastrear/registar o estado de cada chamada para a API dos Serviços de Mídia. [Saiba mais sobre a Azure Cosmos DB](../../cosmos-db/introduction.md).  |
|![Este é o ícone de identidade gerida.](./media/architecture-high-availability-encoding-concept/managed-identity.svg)| Identidade Gerida | **Description:**<br>A identidade gerida é uma característica do Azure AD que fornece uma identidade gerida automaticamente em Azure AD. Pode ser usado para autenticar qualquer serviço que suporte a autenticação AZure AD, incluindo o Key Vault, sem armazenar credenciais em código.<br><br>**Utilização do VOD:**<br>As Funções Azure podem utilizar a Identidade Gerida para autenticar as instâncias dos Serviços de Comunicação Social para ligar ao Key Vault. [Saiba mais sobre Identidade Gerida.](../../active-directory/managed-identities-azure-resources/overview.md) |
|![Este é o ícone do Cofre de Chaves.](./media/architecture-high-availability-encoding-concept/key-vault.svg)| Key Vault | **Description:**<br>O Azure Key Vault pode ser usado para armazenar e controlar de forma segura o acesso a fichas, senhas, certificados, chaves API e outros segredos. Também pode ser usado como uma solução de Gestão de Chaves. O Azure Key Vault torna mais fácil criar e controlar as chaves de encriptação utilizadas para encriptar os seus dados. Pode facilmente providenciar, gerir e implementar certificados de segurança da camada de transporte público e privado/camada de tomadas seguras (TLS/SSL) para utilização com Azure e recursos ligados internos. Segredos e chaves podem ser protegidos por software ou HSMs validados FIPS 140-2 Nível 2.<br><br>**Utilização do VOD:**<br>O Key Vault pode ser usado para configurar políticas de acesso para o principal de serviço para a sua aplicação.  Pode ser usado para armazenar o fio de ligação às contas de armazenamento. Usamos o Key Vault para armazenar cordas de ligação em contas de armazenamento e cosmos db. Também pode utilizar o Key Vault para armazenar a configuração geral do cluster. Para cada Instância de Serviço de Mídia pode armazenar ID de assinatura, nome do grupo de recursos e nome da conta. Para mais detalhes, veja como é usado na amostra. [Saiba mais sobre o Key Vault.](../../key-vault/general/overview.md) |
|![Este é o ícone Azure Functions.](./media/architecture-high-availability-encoding-concept/function-app.svg)| Funções do Azure | **Description:**<br>Executar pequenas peças de código (chamadas "funções") sem se preocupar com a infraestrutura de aplicações com funções Azure. [Saiba mais sobre as Funções Azure](../../azure-functions/functions-overview.md).<br><br>**Utilização do VOD:**<br>As Funções Azure podem ser utilizadas para armazenar os módulos da sua aplicação VOD.  Os módulos para uma aplicação VOD podem incluir:<br><br>**Módulo de Agendamento de Emprego**<br>O módulo de agendamento de empregos seria para a apresentação de novos postos de trabalho num cluster de Serviços de Comunicação Social (dois ou mais casos em diferentes regiões). Acompanharia o estado de saúde de cada instância dos Serviços de Comunicação Social e apresentaria um novo emprego para a próxima instância saudável.<br><br>**Módulo de Estado do Trabalho**<br>O módulo de estado de trabalho estaria a ouvir eventos de estado de saída de emprego provenientes do serviço Azure Event Grid. Armazenaria eventos para a loja de eventos para minimizar o número de chamadas para ASE dos Serviços de Mídia pelo resto dos módulos.<br><br>**Módulo de Saúde de Exemplo**<br>Este módulo acompanharia os postos de trabalho submetidos e determinaria o estado de saúde de cada instância dos Serviços de Comunicação Social. Seguiria empregos acabados, empregos falhados e empregos que nunca terminaram.<br><br>**Módulo de Provisionamento**<br>Este módulo iria provisão de ativos processados. Copiaria dados de ativos a todas as instâncias dos Serviços de Comunicação Social e criaria o serviço Azure Front Door para garantir que os ativos poderiam ser transmitidos mesmo que algumas instâncias dos Serviços de Comunicação Social não estivessem disponíveis. Também criaria localizadores de streaming.<br><br>**Módulo de Verificação de Emprego**<br>Este módulo acompanharia cada trabalho submetido, reenviaria trabalhos falhados e executaria a limpeza dos dados de trabalho assim que um trabalho esteja concluído com sucesso.  |
|![Este é o ícone do Serviço de Aplicações.](./media/architecture-high-availability-encoding-concept/application-service.svg)| Serviço de Aplicações (e plano)  | **Description:**<br>O Azure App Service é um serviço baseado em HTTP para hospedar aplicações web, APIs REST e extremidades traseiras móveis. Suporta .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python. As aplicações são executadas e escala em ambientes baseados no Windows e linux.<br><br>**Utilização do VOD:**<br>Cada módulo seria hospedado por um Serviço de Aplicações. [Saiba mais sobre o Serviço de Aplicações.](../../app-service/overview.md) |
|![Este é o ícone da porta frontal Azure.](./media/architecture-high-availability-encoding-concept/azure-front-door.svg)| Azure Front Door | **Description:**<br>A Azure Front Door é usada para definir, gerir e monitorizar o encaminhamento global do tráfego web, otimizando para o melhor desempenho e rápida falha global para uma elevada disponibilidade.<br><br>**Utilização do VOD:**<br>A Azure Front Door poderia ser usada para encaminhar o tráfego para os pontos finais de streaming. [Saiba mais sobre a Porta frontal Azure.](../../frontdoor/front-door-overview.md)  |
|![Este é o ícone Azure Event Grid.](./media/architecture-high-availability-encoding-concept/event-grid-subscription.svg)| Azure Event Grid | **Description:**<br>Criado para arquiteturas baseadas em eventos, a Event Grid tem apoio incorporado para eventos provenientes de serviços Azure, como bolhas de armazenamento e grupos de recursos. Também tem suporte para eventos temáticos personalizados. Os filtros podem ser usados para encaminhar eventos específicos para diferentes pontos finais, multicast para vários pontos finais, e para garantir que os eventos são entregues de forma fiável. Maximiza a disponibilidade espalhando-se de forma nativa por vários domínios de falhas em todas as regiões, e através de zonas de disponibilidade.<br><br>**Utilização do VOD:**<br>A Grade de Eventos pode ser usada para rastrear todos os eventos de aplicação e armazená-los para persistir no estado do trabalho. [Saiba mais sobre a Azure Event Grid](../../event-grid/overview.md). |
|![Este é o ícone De Insights de Aplicação.](./media/architecture-high-availability-encoding-concept/application-insights.svg)| Application Insights | **Description:** <br>O Application Insights, uma funcionalidade do Azure Monitor, é um serviço extensível de APM (Gestão de Desempenho de Aplicações) para programadores e profissionais do DevOps. É utilizado para monitorizar aplicações ao vivo. Deteta anomalias de desempenho e inclui ferramentas de análise para diagnosticar problemas e para entender o que os utilizadores fazem com uma app. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.<br><br>**Utilização do VOD:**<br>Todos os registos podem ser enviados para Insights de Aplicação. Seria possível ver que instância processou cada trabalho procurando mensagens de emprego criadas com sucesso. Pode conter todos os metadados de trabalho submetidos, incluindo o identificador único e informações sobre o nome de exemplos. [Saiba mais sobre a Aplicação Insights](../../azure-monitor/app/app-insights-overview.md). |
## <a name="architecture"></a>Arquitetura

Este diagrama de alto nível mostra a arquitetura da amostra fornecida para começar com alta disponibilidade e serviços de mídia.

[![Vídeo a pedido (VOD) Diagrama ](media/architecture-high-availability-encoding-concept/high-availability-architecture.svg) de Arquitetura de Alto Nível](./media/architecture-high-availability-encoding-concept/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>Melhores práticas

### <a name="regions"></a>Regiões

* [Criar](./account-create-how-to.md) duas (ou mais) contas Azure Media Services. As duas contas têm de ser em diferentes regiões. Para mais informações, consulte [regiões em que o serviço Azure Media Services está implantado.](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)
* Faça o upload dos seus meios de comunicação para a mesma região a partir da qual planeia submeter o trabalho. Para obter mais informações sobre como começar a codificar, consulte [Criar uma entrada de trabalho a partir de um URL HTTPS](./job-input-from-http-how-to.md) ou [criar uma entrada de trabalho a partir de um ficheiro local](./job-input-from-local-file-how-to.md).
* Se então precisar de reenviar o [trabalho](./transforms-jobs-concept.md) para outra região, pode utilizar `JobInputHttp` ou utilizar para copiar os `Copy-Blob` dados do recipiente de origem Para um contentor de Ativos na região alternativa.

### <a name="monitoring"></a>Monitorização

* Inscreva-se `JobStateChange` para mensagens em cada conta através da Azure Event Grid.
    * [Registe-se para eventos](./reacting-to-media-services-events.md) através do portal Azure ou do CLI (também pode fazê-lo com o SDK de Gestão de Grelhas de Eventos)
    * Utilize o [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (que suporta eventos de Media Services de forma nativa).
    * Também pode consumir eventos de Grade de Eventos através de Funções Azure.

    Para obter mais informações:

    * Consulte a [amostra Audio Analytics](./transforms-jobs-concept.md) que mostra como monitorizar um trabalho com a Azure Event Grid, incluindo a adição de um recuo no caso de as mensagens Azure Event Grid se atrasarem por alguma razão.
    * Veja os esquemas da [Grelha de Eventos Azure para eventos de Serviços de Mídia](./media-services-event-schemas.md).

* Quando se cria um [emprego:](./transforms-jobs-concept.md)
    * Selecione aleatoriamente uma conta da lista de contas atualmente utilizadas (esta lista normalmente conterá ambas as contas, mas se forem detetadas questões pode conter apenas uma conta). Se a lista estiver vazia, levante um alerta para que um operador possa investigar.
    * Crie um registo para acompanhar cada trabalho de voo e a região/conta utilizada.
* Quando o seu `JobStateChange` encarregado receber a notificação de que um trabalho atingiu o estado programado, registe o tempo que entra no estado programado e na região/conta utilizada.
* Quando o seu `JobStateChange` manipulador receber a notificação de que um trabalho atingiu o estado de processamento, marque o registo do trabalho como processamento e registe o tempo que entra no estado de processamento.
* Quando o seu `JobStateChange` manipulador receber a notificação de que um trabalho atingiu um estado final (Terminado/Erro/Cancelado), marque o registo do trabalho adequadamente.
* Tenha um processo separado que periodicamente olha para os seus registos dos trabalhos
    * Se tiver empregos no estado programado que não tenham avançado para o estado de processamento num determinado período de tempo para uma determinada região, retire essa região da sua lista de contas atualmente utilizadas. Dependendo dos requisitos do seu negócio, pode decidir cancelar esses postos de trabalho imediatamente e reencando-os para a outra região. Ou pode dar-lhes mais tempo para se mudarem para o próximo estado.
    * Se uma região foi removida da lista de conta, monitorize-a para recuperação antes de a adicionar de volta à lista. A saúde regional pode ser monitorizada através dos postos de trabalho existentes na região (se não foram cancelados e reenviados), adicionando a conta de volta à lista após um período de tempo, e pelos operadores que monitorizam as comunicações da Azure sobre falhas que podem estar a afetar a Azure Media Services.

## <a name="next-steps"></a>Passos seguintes

* Confira as [amostras de código](/samples/browse/?products=azure-media-services)