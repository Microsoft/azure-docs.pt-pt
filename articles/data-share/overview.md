---
title: O que é o Azure Data Share?
description: Saiba mais sobre a partilha de dados de forma simples e segura a vários clientes e parceiros usando a Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: 363feda1409d2bb54e60d1b2168cba38f2a8a41c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77621859"
---
# <a name="what-is-azure-data-share"></a>O que é o Azure Data Share?

No mundo de hoje, os dados são vistos como um ativo estratégico chave que muitas organizações precisam de partilhar de forma simples e segura com os seus clientes e parceiros. Há muitas maneiras de os clientes fazerem isso hoje, incluindo através de FTP, e-mail, APIs para citar alguns. As organizações podem facilmente perder a noção de com quem partilharam os seus dados. A partilha de dados através do FTP ou através da sua própria infraestrutura API é muitas vezes dispendiosa para fornecer e administrar. Há despesas de gestão associadas a usar estes métodos de partilha em larga escala. 

Muitas organizações têm de ser responsáveis pelos dados que partilharam. Além da prestação de contas, muitas organizações gostariam de ser capazes de controlar, gerir e monitorizar toda a sua partilha de dados de uma forma simples. No mundo de hoje, onde se espera que os dados continuem a crescer a um ritmo exponencial, as organizações precisam de uma forma simples de partilhar os big data. Os clientes exigem os dados mais atualizados para garantir que são capazes de obter insights atempados.

A Azure Data Share permite às organizações partilhar dados de forma simples e segura com vários clientes e parceiros. Em apenas alguns cliques, pode fornecer uma nova conta de partilha de dados, adicionar conjuntos de dados e convidar os seus clientes e parceiros para a sua partilha de dados. Os fornecedores de dados controlam sempre os dados que partilharam. A Azure Data Share torna simples gerir e monitorizar os dados partilhados, quando e por quem. 

Um fornecedor de dados pode manter-se no controlo da forma como os seus dados são tratados especificando os termos de utilização para a sua partilha de dados. O consumidor dos dados tem de aceitar esses termos para os poder receber. Os fornecedores de dados podem especificar a frequência com que os consumidores dos dados recebem atualizações. O acesso a novas atualizações pode ser revogado a qualquer momento pelo fornecedor de dados. 

A Azure Data Share ajuda a melhorar as informações, facilitando a combinação de dados de terceiros para enriquecer cenários de análise e IA. Utilize facilmente o poder das ferramentas de análise Azure para preparar, processar e analisar dados partilhados usando o Azure Data Share. 

Tanto o fornecedor de dados como o consumidor de dados devem ter uma subscrição Azure para partilhar e receber dados. Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/)

## <a name="scenarios-for-azure-data-share"></a>Cenários para a partilha de dados do Azure

A Azure Data Share pode ser usada em várias indústrias diferentes. Por exemplo, um retalhista pode querer partilhar dados recentes de vendas com os seus fornecedores. Utilizando a Azure Data Share, um retalhista pode criar uma quota de dados contendo dados de vendas para todos os seus fornecedores e partilhar vendas de hora a hora ou diariamente. 

A Azure Data Share também pode ser usada para estabelecer um mercado de dados para uma determinada indústria. Por exemplo, um governo ou uma instituição de investigação que partilha regularmente dados anoonizados sobre o crescimento populacional com terceiros. 

Outro caso de utilização para a Azure Data Share é a criação de um consórcio de dados. Por exemplo, várias instituições de investigação podem partilhar dados com um único organismo de confiança. Os dados são analisados, agregados ou processados utilizando ferramentas de análise Azure e depois partilhados com as partes interessadas. 

## <a name="how-it-works"></a>Como funciona

A Azure Data Share oferece atualmente partilha instantânea e partilha no local. 

Na partilha baseada em instantâneos, os dados movem-se da subscrição do fornecedor de dados Azure e aterra na subscrição do Apple e do consumidor de dados. Como fornecedor de dados, você disponibiliza uma partilha de dados e convida os destinatários para a partilha de dados. Os consumidores de dados recebem um convite para a sua partilha de dados por e-mail. Uma vez que um consumidor de dados aceite o convite, pode desencadear uma imagem completa dos dados partilhados com eles. Estes dados são recebidos na conta de armazenamento dos consumidores de dados. Os consumidores de dados podem receber atualizações regulares e incrementais dos dados partilhados com eles para que tenham sempre a versão mais recente dos dados. 

Os fornecedores de dados podem oferecer aos seus consumidores de dados atualizações incrementais aos dados partilhados com eles através de um calendário instantâneo. Os horários instantâneos são oferecidos de hora em hora ou diariamente. Quando um consumidor de dados aceita e confunde a sua quota de dados, pode subscrever um calendário instantâneo. Isto é benéfico em cenários em que os dados partilhados são atualizados regularmente, e os dados que o consumidor precisa dos dados mais atualizados. 

![fluxo de partilha de dados](media/data-share-flow.png)

Quando um consumidor de dados aceita uma partilha de dados, pode receber os dados numa loja de dados à sua escolha. Por exemplo, se o fornecedor de dados partilhar dados utilizando o Armazenamento De Blob Azure, o consumidor de dados pode receber estes dados na Azure Data Lake Store. Da mesma forma, se o fornecedor de dados partilhar dados de um Armazém de Dados Azure SQL, o consumidor de dados pode escolher se quer receber os dados numa Loja de Lagos De Dados Azure, numa Base de Dados Azure SQL ou num Armazém de Dados Azure SQL. No caso de partilha de fontes baseadas em SQL, o consumidor de dados também pode escolher se recebe dados em parquet ou csv. 

Com a partilha no local, os fornecedores de dados podem partilhar dados onde residem sem copiar os dados. Após a partilha da relação é estabelecida através do fluxo de convite, é criada uma ligação simbólica entre a loja de dados de origem do fornecedor de dados e a loja de dados-alvo do consumidor de dados. O consumidor de dados pode ler e consultar os dados em tempo real usando a sua própria loja de dados. As alterações na loja de dados de origem estão imediatamente disponíveis para o consumidor de dados. A partilha no local está atualmente em pré-visualização do Azure Data Explorer.

## <a name="key-capabilities"></a>Principais capacidades

A Azure Data Share permite aos fornecedores de dados:

* Partilhe dados da lista de lojas de [dados suportadas](supported-data-stores.md) com clientes e parceiros fora da sua organização

* Acompanhe com quem partilhou os seus dados

* Escolha de snapshot ou partilha no local

* Com que frequência os consumidores de dados estão a receber atualizações dos seus dados

* Permita que os seus clientes puxem a versão mais recente dos seus dados conforme necessário, ou permitam que recebam automaticamente alterações incrementais dos seus dados num intervalo definido por si

A Azure Data Share permite aos consumidores de dados: 

* Ver uma descrição do tipo de dados que estão a ser partilhados

* Ver termos de utilização para os dados

* Aceitar ou rejeitar um convite azure Data Share

* Aceite os dados partilhados consigo numa loja de [dados suportada.](supported-data-stores.md)

* Desencadear uma imagem completa ou incremental de uma Data Share que uma organização partilhou consigo

* Subscreva uma partilha de dados para receber a mais recente cópia dos dados através de instantâneo incremental

Todas as principais capacidades listadas acima são suportadas através do portal Azure ou através de APIs REST. Para mais detalhes sobre a utilização da Partilha de Dados do Azure através de APIs REST, consulte a nossa documentação de referência. 

## <a name="security"></a>Segurança

A Azure Data Share aproveita a segurança subjacente que o Azure oferece para proteger os dados em repouso e em trânsito. Os dados são encriptados em repouso, onde suportados pela loja de dados subjacente. Os dados também são encriptados em trânsito. Os metadados sobre uma partilha de dados também são encriptados em repouso e em trânsito. 

Os controlos de acesso podem ser definidos no nível de recurso Azure Data Share para garantir que são acedidos por aqueles que são autorizados. 

A Azure Data Share aproveita as identidades geridas para os recursos azure (anteriormente conhecidos como MSIs) para a gestão automática de identidades no Diretório Ativo azure. As identidades geridas para os Recursos Azure são alavancadas para o acesso às lojas de dados que estão a ser utilizadas para a partilha de dados. Não existe troca de credenciais entre um fornecedor de dados e um consumidor de dados. Para mais informações, consulte a [página Identidades Geridas para Recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 


## <a name="supported-regions"></a>Regiões suportadas

Para obter uma lista das regiões do Azure que disponibilizam a Azure Data Share, consulte os produtos disponíveis por página da [região](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) e procure a Partilha de Dados do Azure. 

A Azure Data Share não armazena uma cópia dos dados em si. Os dados são armazenados na loja de dados subjacente que está a ser partilhada. Por exemplo, se um produtor de dados armazenar os seus dados numa conta azure Data Lake Store localizada no Oeste dos EUA, é aí que os dados são armazenados. Se estiverem a partilhar dados com uma conta de Armazenamento Azure localizada na Europa Ocidental através de instantâneo, normalmente os dados são transferidos diretamente para a conta de Armazenamento Azure localizada na Europa Ocidental.

O serviço Azure Data Share não tem de estar disponível na sua região para alavancar o serviço. Por exemplo, se tiver dados armazenados numa conta de Armazenamento Azure localizada numa região onde a Azure Data Share ainda não está disponível, ainda pode aproveitar o serviço para partilhar os seus dados. 

## <a name="next-steps"></a>Passos seguintes

Para aprender a começar a partilhar dados, continue a partilhar o seu tutorial de [dados.](share-your-data.md)
