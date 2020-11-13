---
title: O que é o Azure Data Share?
description: Saiba como partilhar dados de forma simples e segura a vários clientes e parceiros usando a Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: overview
ms.date: 10/30/2020
ms.openlocfilehash: b9fb5d6537d2c8dffef397c56128dd4891c939b7
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578132"
---
# <a name="what-is-azure-data-share"></a>O que é o Azure Data Share?

No mundo de hoje, os dados são vistos como um ativo estratégico chave que muitas organizações precisam de partilhar de forma simples e segura com os seus clientes e parceiros. Há muitas maneiras de os clientes fazerem isso hoje, incluindo através de FTP, e-mail, APIs para citar alguns. As organizações podem facilmente perder a noção de com quem partilharam os seus dados. A partilha de dados através da FTP ou através da criação da sua própria infraestrutura API é muitas vezes dispendiosa para a oferta e administração. Há uma sobrecarga de gestão associada a usar estes métodos de partilha em larga escala. 

Muitas organizações precisam de ser responsabilizadas pelos dados que partilharam. Além da prestação de contas, muitas organizações gostariam de ser capazes de controlar, gerir e monitorizar toda a sua partilha de dados de uma forma simples. No mundo de hoje, onde se espera que os dados continuem a crescer a um ritmo exponencial, as organizações precisam de uma forma simples de partilhar grandes dados. Os clientes exigem os dados mais atualizados para garantir que são capazes de obter insights oportunos.

O Azure Data Share permite que as organizações partilhem dados de forma simples e segura com vários clientes e parceiros. Em apenas alguns cliques, pode providenciar uma nova conta de partilha de dados, adicionar conjuntos de dados e convidar os seus clientes e parceiros para a sua partilha de dados. Os fornecedores de dados controlam sempre os dados que partilharam. A Azure Data Share torna simples gerir e monitorizar os dados partilhados, quando e por quem. 

Um fornecedor de dados pode manter-se no controlo da forma como os seus dados são tratados, especificando termos de utilização para a sua partilha de dados. O consumidor dos dados tem de aceitar esses termos para os poder receber. Os fornecedores de dados podem especificar a frequência com que os consumidores dos dados recebem atualizações. O acesso a novas atualizações pode ser revogado a qualquer momento pelo fornecedor de dados. 

O Azure Data Share ajuda a melhorar os insights, facilitando a combinação de dados de terceiros para enriquecer a análise e os cenários de IA. Utilize facilmente o poder das ferramentas de análise Azure para preparar, processar e analisar dados partilhados através do Azure Data Share. 

Tanto o fornecedor de dados como o consumidor de dados devem ter uma subscrição Azure para partilhar e receber dados. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Cenários para Azure Data Share

A Azure Data Share pode ser usada em várias indústrias diferentes. Por exemplo, um retalhista pode querer partilhar dados recentes de vendas com os seus fornecedores. Utilizando a Azure Data Share, um retalhista pode configurar uma partilha de dados contendo dados de venda para todos os seus fornecedores e partilhar vendas de hora a hora ou diariamente. 

O Azure Data Share também pode ser usado para estabelecer um mercado de dados para uma indústria específica. Por exemplo, um governo ou uma instituição de investigação que partilha regularmente dados anonimizados sobre o crescimento da população com terceiros. 

Outro caso de utilização da Azure Data Share é a criação de um consórcio de dados. Por exemplo, várias instituições de investigação podem partilhar dados com um único organismo de confiança. Os dados são analisados, agregados ou processados utilizando ferramentas de análise Azure e depois partilhados com as partes interessadas. 

## <a name="how-it-works"></a>Como funciona

A Azure Data Share oferece atualmente partilha baseada em instantâneos e partilha no local. 

Na partilha baseada em instantâneos, os dados movem-se da subscrição Azure do fornecedor de dados e aterram na subscrição Azure do consumidor de dados. Como fornecedor de dados, fornece uma partilha de dados e convida os destinatários para a partilha de dados. Os consumidores de dados recebem um convite para a sua partilha de dados por e-mail. Uma vez que um consumidor de dados aceite o convite, eles podem desencadear uma imagem completa dos dados partilhados com eles. Estes dados são recebidos na conta de armazenamento de consumidores de dados. Os consumidores de dados podem receber atualizações regulares e incrementais aos dados partilhados com eles para que tenham sempre a versão mais recente dos dados. 

Os fornecedores de dados podem oferecer aos seus consumidores de dados atualizações incrementais aos dados partilhados com eles através de um calendário instantâneo. Os horários instantâneos são oferecidos diariamente ou à hora. Quando um consumidor de dados aceita e configura a sua partilha de dados, pode subscrever um calendário instantâneo. Isto é benéfico em cenários em que os dados partilhados são atualizados regularmente, e o consumidor de dados precisa dos dados mais atualizados. 

![fluxo de partilha de dados](media/data-share-flow.png)

Quando um consumidor de dados aceita uma partilha de dados, pode receber os dados numa loja de dados à sua escolha. Por exemplo, se o fornecedor de dados partilhar dados usando o Azure Blob Storage, o consumidor de dados pode receber estes dados na Azure Data Lake Store. Da mesma forma, se o fornecedor de dados partilhar dados de um Azure Synapse Analytics, o consumidor de dados pode escolher se quer receber os dados numa Loja de Data Lake Azure, numa Base de Dados Azure SQL ou numa Azure Synapse Analytics. No caso de partilha de fontes baseadas em SQL, o consumidor de dados também pode escolher se recebe dados em parquet ou csv. 

Com a partilha no local, os fornecedores de dados podem partilhar dados onde residem sem copiar os dados. Após a partilha da relação é estabelecida através do fluxo de convites, é criada uma ligação simbólica entre a loja de dados de origem do fornecedor de dados e a loja de dados-alvo do consumidor de dados. O consumidor de dados pode ler e consultar os dados em tempo real utilizando a sua própria loja de dados. As alterações na loja de dados de origem estão imediatamente disponíveis para o consumidor de dados. A partilha no local está atualmente em pré-visualização para o Azure Data Explorer.

## <a name="key-capabilities"></a>Principais capacidades

A Azure Data Share permite que os fornecedores de dados:

* Partilhe dados da lista de lojas de [dados suportadas](supported-data-stores.md) com clientes e parceiros fora da sua organização

* Acompanhe com quem partilhou os seus dados

* Escolha da partilha instantânea ou no local

* Com que frequência os consumidores de dados estão a receber atualizações aos seus dados

* Permita que os seus clientes retirem a versão mais recente dos seus dados conforme necessário, ou permita que recebam automaticamente alterações incrementais nos seus dados num intervalo definido por si

A Azure Data Share permite que os consumidores de dados: 

* Ver uma descrição do tipo de dados partilhados

* Ver termos de utilização para os dados

* Aceite ou rejeite um convite de partilha de dados Azure

* Aceite os dados partilhados consigo numa [loja de dados suportada.](supported-data-stores.md)

* Aceder aos dados no local ou desencadear uma imagem completa ou incremental dos dados partilhados

Todas as capacidades-chave listadas acima são suportadas através do portal Azure ou através de REST APIs. Para mais detalhes sobre a utilização do Azure Data Share através de REST APIs, consulte a nossa documentação de referência. 

## <a name="supported-regions"></a>Regiões suportadas

Para obter uma lista de regiões do Azure que disponibilizam a Azure Data Share, consulte os [produtos disponíveis por página da região](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) e procure a Azure Data Share. 

A Azure Data Share não armazena uma cópia dos dados em si. Os dados são armazenados na loja de dados subjacente que está a ser partilhada. Por exemplo, se um produtor de dados armazenar os seus dados numa conta Azure Data Lake Store localizada no Oeste dos EUA, é aí que os dados são armazenados. Se estiverem a partilhar dados com uma conta de Armazenamento Azure localizada na Europa Ocidental através de snapshot, normalmente os dados são transferidos diretamente para a conta de Armazenamento Azure localizada na Europa Ocidental.

O serviço Azure Data Share não precisa de estar disponível na sua região para alavancar o serviço. Por exemplo, se tiver dados armazenados numa conta de Armazenamento Azure localizada numa região onde o Azure Data Share ainda não está disponível, ainda pode aproveitar o serviço para partilhar os seus dados. 

## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)
