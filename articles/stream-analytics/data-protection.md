---
title: Proteção de dados em Azure Stream Analytics
description: Este artigo explica como encriptar os seus dados privados utilizados por um trabalho do Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 72566987068729efef4310ce145c30584c4895b0
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980512"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Proteção de dados em Azure Stream Analytics 

O Azure Stream Analytics é uma plataforma totalmente gerida como um serviço que lhe permite construir oleodutos de análise em tempo real. Todo o levantamento pesado, como o fornecimento de clusters, os nós de escala para acomodar o seu uso, e a gestão de postos de controlo internos, é gerido nos bastidores.

## <a name="private-data-assets-that-are-stored"></a>Ativos de dados privados que são armazenados

O Azure Stream Analytics persiste nos seguintes metadados e dados para executar: 

* Definição de consulta e sua configuração relacionada  

* Funções ou agregados definidos pelo utilizador  

* Pontos de verificação necessários pelo tempo de funcionamento do Stream Analytics

* Instantâneos de dados de referência 

* Detalhes de conexão dos recursos utilizados pelo seu trabalho stream Analytics

Para ajudá-lo a cumprir as suas obrigações de conformidade em qualquer indústria ou ambiente regulamentado, pode ler mais sobre [as ofertas de conformidade da Microsoft.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 

## <a name="in-region-data-residency"></a>Residência de Dados In-Region
O Azure Stream Analytics armazena os dados do cliente e outros metadados acima descritos. Os dados do cliente são armazenados pela Azure Stream Analytics numa única região por padrão, pelo que este serviço satisfaz automaticamente os requisitos de residência de dados da região, incluindo os especificados no [Trust Center.](https://azuredatacentermap.azurewebsites.net/)
Além disso, pode optar por armazenar todos os ativos de dados (dados do cliente e outros metadados) relacionados com o seu trabalho de análise de fluxo numa única região, encriptando-os numa conta de armazenamento à sua escolha.

## <a name="encrypt-your-data"></a>Encriptar os dados

O Stream Analytics utiliza automaticamente os melhores padrões de encriptação em classe em toda a sua infraestrutura para encriptar e proteger os seus dados. Pode simplesmente confiar no Stream Analytics para armazenar de forma segura todos os seus dados para que não tenha de se preocupar em gerir a infraestrutura.

Se pretender utilizar as teclas geridas pelo cliente (CMK) para encriptar os seus dados, pode utilizar a sua própria conta de armazenamento (finalidade geral V1 ou V2) para armazenar quaisquer ativos de dados privados que sejam exigidos pelo tempo de execução do Stream Analytics. A sua conta de armazenamento pode ser encriptada conforme necessário. Nenhum dos seus ativos de dados privados é armazenado permanentemente pela infraestrutura Stream Analytics. 

Esta definição deve ser configurada no momento da criação de emprego stream Analytics, e não pode ser modificada ao longo do ciclo de vida do trabalho. Não é recomendada modificação ou eliminação do armazenamento que está a ser utilizado pelo stream Analytics. Se eliminar a sua conta de armazenamento, eliminará permanentemente todos os ativos de dados privados, o que fará com que o seu trabalho falhe. 

A atualização ou rotação das teclas da sua conta de armazenamento não é possível utilizando o portal Stream Analytics. Pode atualizar as teclas utilizando as APIs REST.


### <a name="configure-storage-account-for-private-data"></a>Configure a conta de armazenamento de dados privados 


Criptografe a sua conta de armazenamento para proteger todos os seus dados e escolha explicitamente a localização dos seus dados privados. 

Para ajudá-lo a cumprir as suas obrigações de conformidade em qualquer indústria ou ambiente regulamentado, pode ler mais sobre [as ofertas de conformidade da Microsoft.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 



Utilize os seguintes passos para configurar a sua conta de armazenamento para ativos de dados privados. Esta configuração é feita a partir do seu trabalho stream Analytics, não da sua conta de armazenamento.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure. 

1. Selecione o trabalho **analytics**   >  **stream analytics**   da lista de resultados. 

1. Preencha a página de trabalho stream Analytics com detalhes necessários, como nome, região e escala. 

1. Selecione a caixa de verificação que diz *Proteger todos os ativos de dados privados necessários por este trabalho na minha conta de Armazenamento.*

1. Selecione uma conta de armazenamento da sua subscrição. Note que esta definição não pode ser modificada durante todo o ciclo de vida do trabalho. 

   ![Definições privadas de conta de armazenamento de dados](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>Ativos de dados privados que são armazenados pela Stream Analytics

Quaisquer dados privados que sejam necessários para serem persistidos pelo Stream Analytics são armazenados na sua conta de armazenamento. Exemplos de ativos de dados privados incluem: 

* Consultas que autorizou e as suas configurações relacionadas  

* Funções definidas pelo utilizador 

* Pontos de verificação necessários pelo tempo de funcionamento do Stream Analytics

* Instantâneos de dados de referência 

Os detalhes de ligação dos seus recursos, que são utilizados pelo seu trabalho stream Analytics, também estão armazenados. Criptografe a sua conta de armazenamento para proteger todos os seus dados. 

Para ajudá-lo a cumprir as suas obrigações de conformidade em qualquer indústria ou ambiente regulamentado, pode ler mais sobre [as ofertas de conformidade da Microsoft.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 

## <a name="enables-data-residency"></a>Permite residência de dados 
Pode utilizar esta funcionalidade para impor quaisquer requisitos de residência de dados que possa ter fornecendo uma conta de armazenamento em conformidade.

## <a name="known-issues"></a>Problemas conhecidos
Existe um problema conhecido em que um trabalho que utiliza a chave gerida pelo cliente encontra falhas ao usar a identidade gerida para autenticar quaisquer entradas ou saídas. 

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md)
* [Compreenda as entradas para a Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Checkpoint e replay conceitos em empregos Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Utilização de dados de referência para procuras no Stream Analytics](stream-analytics-use-reference-data.md)
