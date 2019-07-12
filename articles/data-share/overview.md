---
title: O que é a pré-visualização de partilha de dados do Azure
description: Uma descrição geral da pré-visualização de partilha de dados do Azure
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 7d4e51ec9564bfb123cf73d9fe89d040f42fe650
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807550"
---
# <a name="what-is-azure-data-share-preview"></a>O que é a Pré-visualização do Azure Data Share?

No mundo de hoje, os dados são apresentados como um ativo estratégico essencial que muitas organizações precisam simplesmente e segura compartilhar com seus clientes e parceiros. Existem várias formas que os clientes fazer este hoje em dia, incluindo através de FTP, email, as APIs para citar alguns. As organizações facilmente podem perder o controle de quem estas os partilharam seus dados com. Partilha de dados através de FTP ou permanente a sua própria infraestrutura de API costuma ser caro para provisionar e administrar. Não há custos de gestão associado à utilização desses métodos de compartilhamento em grande escala. 

Muitas organizações tem responsabilizar-se de que para os dados que partilharam. Além de responsabilidade, muitas organizações gostaria de conseguir controlar, gerir e monitorizar todos os dados de partilha de uma forma simples. No mundo de hoje, onde os dados são esperados para continuar a crescer um ritmo exponencial, as organizações precisam de uma forma simple de compartilhar grandes quantidades de dados. Os clientes exigem que os dados mais atualizados para garantir que eles são capazes derivar informações atempadamente.

A pré-visualização de partilha de dados do Azure permite às organizações simplesmente e segura partilhar dados com vários clientes e parceiros. Em apenas alguns cliques, pode aprovisionar uma nova conta de partilha de dados, adicionar conjuntos de dados e convidar os seus clientes e parceiros para a partilha de dados. Provedores de dados são sempre no controlo dos dados que partilharam. Partilha de dados do Azure torna simples para gerir e monitorizar os dados que foi partilhados, quando e por quem. 

Um provedor de dados pode manter o controle de como os seus dados são processados pela especificação de termos de utilização para a sua partilha de dados. O consumidor de dados tem de aceitar estes termos antes de poder receber os dados. Provedores de dados, podem especificar a frequência com que seus consumidores de dados recebem atualizações. Acesso a novas atualizações pode ser revogado a qualquer momento pelo fornecedor de dados. 

Partilha de dados do Azure ajuda a melhorar o insights, que facilita a combinar dados de terceiros para enriquecer a análise e cenários de IA. Utilize facilmente as ferramentas de análise do Azure power utras para preparar, processar e analisar os dados partilhados com a partilha de dados do Azure. 

## <a name="scenarios-for-azure-data-share"></a>Cenários para partilha de dados do Azure

Partilha de dados do Azure pode ser utilizada em vários setores diferentes. Por exemplo, um varejista talvez queira partilhar recente ponto de dados de vendas com seus fornecedores. Utilizar a partilha de dados do Azure, um varejista, pode configurar uma partilha de dados que contém o ponto de dados de vendas para todos os seus fornecedores e partilhar vendas numa base por hora ou diariamente. 

Partilha de dados do Azure também pode ser utilizada para estabelecer um mercado de dados para um setor específico. Por exemplo, um governo ou uma instituição de pesquisa que partilha regularmente dados anónimos sobre o crescimento de população com terceiros. 

Outro caso de utilização para a partilha de dados do Azure é estabelecer um consórcio de dados. Por exemplo, um número de instituições de investigação diferentes pode partilhar dados com um corpo de confiança único. Dados são analisados, agregado ou processada ao utilizar ferramentas de análise do Azure e, em seguida, partilhado com partes interessadas. 

## <a name="how-it-works"></a>Como funciona

Partilha de dados do Azure utiliza uma instantâneo de partilha abordagem baseada, onde os dados se move de subscrição do Azure do fornecedor de dados e na subscrição do Azure do consumidor de dados que chegam. Como um provedor de dados, pode aprovisionar uma partilha de dados e convidar os destinatários para a partilha de dados. Os consumidores de dados recebem um convite para a partilha de dados por email. Assim que um consumidor de dados aceita o convite, podem acionar um instantâneo dos dados partilhados que partilhou. Estes dados são recebidos para a conta de armazenamento de consumidores de dados. Os consumidores de dados podem receber atualizações regulares e incrementais dos dados partilhados com eles para que tenham sempre a versão mais recente dos dados. 

Provedores de dados podem oferecer a seus dados atualizações incrementais de consumidores para os dados partilhados com eles através de uma agenda de instantâneo. As agendas de instantâneo são oferecidas numa hora a hora ou diariamente. Quando um consumidor de dados aceita e configura a sua partilha de dados, eles podem subscrever uma agenda de instantâneo. Isso é benéfico em cenários onde os dados partilhados são atualizados regularmente, e o consumidor de dados tem dos dados mais atualizados. 

![fluxo de partilha de dados](media/data-share-flow.png)

Quando um consumidor de dados aceita uma partilha de dados, eles são capazes de receber os dados numa conta de armazenamento à sua escolha. Por exemplo, se o fornecedor de dados, partilhar dados com o armazenamento de Blobs do Azure, o consumidor de dados pode receber estes dados no Azure Data Lake Store. 

## <a name="key-capabilities"></a>Principais capacidades

Partilha de dados do Azure permite aos fornecedores de dados:

* Partilhar os dados do armazenamento do Azure e Azure Data Lake Store com clientes e parceiros fora da sua organização

* Mantenha um registo de quem partilhou os dados com o

* A frequência com que os consumidores de dados estão a receber atualizações aos seus dados

* Permitir que os seus clientes solicitar a versão mais recente dos seus dados conforme necessário, ou permitir que os mesmos receber automaticamente as alterações incrementais aos seus dados num intervalo definido por si

Partilha de dados do Azure permite que os consumidores de dados para: 

* Ver uma descrição do tipo de dados que está a ser partilhados

* Ver termos de utilização para os dados

* Aceitar ou rejeitar um convite de partilha de dados do Azure

* Acionar um instantâneo completo ou incremental de uma partilha de dados que uma organização tenha partilhado consigo

* Subscrever uma partilha de dados para receber a cópia mais recente dos dados através da cópia de instantâneo incremental

* Aceitar dados partilhados para uma conta de armazenamento de Blobs do Azure ou de geração 2 do Azure Data Lake

Todos os recursos importantes listados acima são suportados através do Azure ou através de REST APIs. Para obter mais detalhes sobre como utilizar a partilha de dados do Azure através de REST APIs, consulte nossa documentação de referência. 

## <a name="security"></a>Segurança

Partilha de dados do Azure tira partido da segurança subjacente que o Azure oferece para proteger os dados inativos e em trânsito. Os dados são encriptados em inatividade, onde for suportado pelo mecanismo de armazenamento subjacente. Dados também são encriptados em trânsito. Metadados sobre uma partilha de dados também são encriptados em descanso e em trânsito. 

Controlos de acesso podem ser definidos ao nível do recurso de partilha de dados do Azure para garantir que ele é acessado por aqueles que estão autorizadas. 

Partilha de dados do Azure tira partido de identidades geridas por um para recursos do Azure (anteriormente conhecido como MSIs) para a gestão de identidades automática no Azure Active Directory. Identidades geridas para recursos do Azure são aproveitadas para acesso às contas de armazenamento que estão a ser utilizado para a partilha de dados. Não há nenhum exchange de credenciais entre um provedor de dados e um consumidor de dados. Para obter mais informações, consulte a [identidades geridas por um para a página de recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 

## <a name="supported-regions"></a>Regiões suportadas

Para obter uma lista de regiões do Azure, que disponibilizam a partilha de dados do Azure, consulte a [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) página e procure a partilha de dados do Azure. 

Partilha de dados do Azure não armazena quaisquer dados em si. Os dados são armazenados em contas de armazenamento subjacentes que estão a ser partilhadas. Por exemplo, se um produtor de dados armazena seus dados numa conta do Azure Data Lake Store localizada na região E.U.A. oeste, que é onde os dados são armazenados. Se eles estão a partilhar dados com uma conta de armazenamento do Azure localizada na Europa Ocidental, os dados são transferidos diretamente para a conta de armazenamento do Azure localizada na Europa Ocidental. 

O serviço de partilha de dados do Azure não tem de estar disponível na sua região para tirar partido do serviço. Por exemplo, se tiver dados armazenados numa conta de armazenamento do Azure localizada numa região onde a partilha de dados do Azure ainda não está disponível, pode ainda aproveitar o serviço para partilhar os seus dados. 

## <a name="next-steps"></a>Passos Seguintes

Para saber como começar a partilha de dados, avance para o [partilhar os seus dados](share-your-data.md) tutorial.
