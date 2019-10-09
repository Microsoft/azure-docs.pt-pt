---
title: O que é a versão prévia do compartilhamento de dados do Azure
description: Saiba como compartilhar dados de maneira simples e segura com vários clientes e parceiros usando a visualização do compartilhamento de dados do Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: 6ba6e9df7b3fd2992891f4b085581c51fe900744
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169078"
---
# <a name="what-is-azure-data-share-preview"></a>O que é a Pré-visualização do Azure Data Share?

No mundo atual, os dados são exibidos como um ativo estratégico importante, que muitas organizações precisam compartilhar de forma simples e segura com seus clientes e parceiros. Há muitas maneiras pelas quais os clientes fazem isso hoje, incluindo por meio de FTP, email, APIs para citar alguns. As organizações podem perder facilmente o controle de quem compartilhou seus dados. O compartilhamento de dados por FTP ou por meio da sua própria infraestrutura de API é muitas vezes caro para provisionar e administrar. Há uma sobrecarga de gerenciamento associada ao uso desses métodos de compartilhamento em grande escala. 

Muitas organizações precisam ser contadas para os dados que eles compartilharam. Além da responsabilidade, muitas organizações gostariam de poder controlar, gerenciar e monitorar todo o compartilhamento de dados de forma simples. No mundo atual, onde os dados devem continuar crescendo em um ritmo exponencial, as organizações precisam de uma maneira simples de compartilhar Big Data. Os clientes exigem os dados mais atualizados para garantir que eles possam derivar informações oportunas.

A visualização do compartilhamento de dados do Azure permite que as organizações compartilhem dados de forma simples e segura com vários clientes e parceiros. Com apenas alguns cliques, você pode provisionar uma nova conta de compartilhamento de dados, Adicionar conjuntos e convidar seus clientes e parceiros para seu compartilhamento de dados. Os provedores de dados sempre estão no controle dos dados que eles compartilharam. O compartilhamento de dados do Azure torna simples gerenciar e monitorar quais dados foram compartilhados, quando e por quem. 

Um provedor de dados pode manter o controle de como seus dados são manipulados especificando os termos de uso para seu compartilhamento de dados. O consumidor de dados deve aceitar esses termos antes de poder receber os dados. Os provedores de dados podem especificar a frequência em que seus consumidores de dados recebem atualizações. O acesso a novas atualizações pode ser revogado a qualquer momento pelo provedor de dados. 

O compartilhamento de dados do Azure ajuda a aprimorar as ideias, facilitando a combinação de dados de terceiros para enriquecer os cenários de análise e ia. Use facilmente o poder de ferramentas de análise do Azure para preparar, processar e analisar dados compartilhados usando o compartilhamento de dados do Azure. 

O provedor de dados e o consumidor de dados devem ter uma assinatura do Azure para compartilhar e receber dados. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Cenários para o compartilhamento de dados do Azure

O compartilhamento de dados do Azure pode ser usado em vários setores diferentes. Por exemplo, um varejista pode querer compartilhar dados do ponto de vendas recentes com seus fornecedores. Usando o compartilhamento de dados do Azure, um varejista pode configurar um compartilhamento de dados que contém dados de ponto de vendas para todos os seus fornecedores e compartilhar vendas por hora ou diariamente. 

O compartilhamento de dados do Azure também pode ser usado para estabelecer um Marketplace de dados para um setor específico. Por exemplo, um governo ou uma instituição de pesquisa que compartilha regularmente dados anônimos sobre o crescimento da população com terceiros. 

Outro caso de uso para o compartilhamento de dados do Azure é estabelecer um consórcio de dados. Por exemplo, várias instituições de pesquisa diferentes podem compartilhar dados com um único corpo confiável. Os dados são analisados, agregados ou processados usando as ferramentas de análise do Azure e, em seguida, compartilhados com partes interessadas. 

## <a name="how-it-works"></a>Como funciona

O compartilhamento de dados do Azure usa uma abordagem de compartilhamento baseada em instantâneo, em que os dados se movem da assinatura do Azure do provedor de dados e atingem a assinatura do Azure do consumidor de dados. Como um provedor de dados, você provisiona um compartilhamento de dados e convida os destinatários para o compartilhamento de dados. Os consumidores de dados recebem um convite para seu compartilhamento de dados por email. Depois que um consumidor de dados aceita o convite, ele pode disparar um instantâneo completo dos dados compartilhados que você os compartilhou. Esses dados são recebidos na conta de armazenamento de consumidores de dados. Os consumidores de dados podem receber atualizações regulares e incrementais para os dados compartilhados com eles para que sempre tenham a versão mais recente dos dados. 

Os provedores de dados podem oferecer as atualizações incrementais dos consumidores de dados aos dados compartilhados com eles por meio de um agendamento de instantâneo. As agendas de instantâneo são oferecidas por hora ou diariamente. Quando um consumidor de dados aceita e configura seu compartilhamento de dados, eles podem assinar uma agenda de instantâneo. Isso é benéfico em cenários em que os dados compartilhados são atualizados regularmente e o consumidor de dados precisa dos dados mais atualizados. 

![fluxo de compartilhamento de dados](media/data-share-flow.png)

Quando um consumidor de dados aceita um compartilhamento de dados, eles são capazes de receber os dados em uma conta de armazenamento de sua escolha. Por exemplo, se o provedor de dados compartilhar dados usando o armazenamento de BLOBs do Azure, o consumidor de dados poderá receber esses dados em Azure Data Lake Store. 

## <a name="key-capabilities"></a>Principais capacidades

O compartilhamento de dados do Azure permite que os provedores de dados:

* Compartilhar dados do armazenamento do Azure e Azure Data Lake Store com clientes e parceiros fora da sua organização

* Mantenha o controle de quem compartilhou seus dados

* Com que frequência seus consumidores de dados estão recebendo atualizações para seus dados

* Permita que seus clientes recebam a versão mais recente dos seus dados conforme necessário ou permita que eles recebam automaticamente alterações incrementais em seus dados em um intervalo definido por você

O compartilhamento de dados do Azure permite que os consumidores de dados: 

* Exibir uma descrição do tipo de dados que está sendo compartilhado

* Exibir os termos de uso dos dados

* Aceitar ou rejeitar um convite de compartilhamento de dados do Azure

* Disparar um instantâneo completo ou incremental de um compartilhamento de dados que uma organização compartilhou com você

* Assinar um compartilhamento de dados para receber a cópia mais recente dos dados por meio da cópia de instantâneo incremental

* Aceitar dados compartilhados com você em um armazenamento de BLOBs do Azure ou Azure Data Lake conta do Gen2

Todos os principais recursos listados acima têm suporte por meio do Azure ou por meio de APIs REST. Para obter mais detalhes sobre como usar o compartilhamento de dados do Azure por meio de APIs REST, Confira nossa documentação de referência. 

## <a name="security"></a>Segurança

O compartilhamento de dados do Azure aproveita a segurança subjacente que o Azure oferece para proteger os dados em repouso e em trânsito. Os dados são criptografados em repouso, onde o mecanismo de armazenamento subjacente dá suporte. Os dados também são criptografados em trânsito. Os metadados sobre um compartilhamento de dados também são criptografados em repouso e em trânsito. 

Os controles de acesso podem ser definidos no nível de recurso de compartilhamento de dados do Azure para garantir que eles sejam acessados por aqueles autorizados. 

O compartilhamento de dados do Azure aproveita identidades gerenciadas para recursos do Azure (anteriormente conhecidos como MSIs) para o gerenciamento automático de identidades no Azure Active Directory. Identidades gerenciadas para recursos do Azure são aproveitadas para acesso às contas de armazenamento que estão sendo usadas para o compartilhamento de dados. Não há nenhuma troca de credenciais entre um provedor de dados e um consumidor de dados. Para obter mais informações, consulte a [página identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 


## <a name="supported-regions"></a>Regiões suportadas

Para obter uma lista de regiões do Azure que disponibilizam o compartilhamento de dados do Azure, consulte a página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) e pesquise compartilhamento de dados do Azure. 

O compartilhamento de dados do Azure não armazena nenhum dado. Os dados são armazenados nas contas de armazenamento subjacentes que estão sendo compartilhadas. Por exemplo, se um produtor de dados armazena seus dados em uma conta de Azure Data Lake Store localizada no oeste dos EUA, é aí que os dados são armazenados. Se eles estiverem compartilhando dados com uma conta de armazenamento do Azure localizada em Europa Ocidental, os dados serão transferidos diretamente para a conta de armazenamento do Azure localizada em Europa Ocidental. 

O serviço de compartilhamento de dados do Azure não precisa estar disponível em sua região para aproveitar o serviço. Por exemplo, se você tiver dados armazenados em uma conta de armazenamento do Azure localizada em uma região em que o compartilhamento de dados do Azure ainda não está disponível, você ainda poderá aproveitar o serviço para compartilhar seus dados. 

## <a name="next-steps"></a>Passos seguintes

Para saber como começar a compartilhar dados, continue no tutorial [compartilhar seus dados](share-your-data.md) .
