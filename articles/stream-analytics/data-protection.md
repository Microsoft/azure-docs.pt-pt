---
title: Proteção de dados em Azure Stream Analytics
description: Este artigo explica como encriptar os seus dados privados utilizados por um trabalho da Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 8d201beb2ff4aba815749b12a506d2292779cb82
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857281"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Proteção de dados em Azure Stream Analytics 

O Azure Stream Analytics é uma plataforma totalmente gerida como um serviço que lhe permite construir pipelines de análise em tempo real. Todo o levantamento pesado, como o fornecimento de clusters, a escala de nós para acomodar o seu uso, e a gestão de postos de controlo internos, é gerido nos bastidores.

## <a name="encrypt-your-data"></a>Encriptar os dados

O Stream Analytics emprega automaticamente padrões de encriptação de melhor classe em toda a sua infraestrutura para encriptar e proteger os seus dados. Pode simplesmente confiar no Stream Analytics para armazenar de forma segura todos os seus dados para que não tenha de se preocupar em gerir a infraestrutura.

Se pretender utilizar chaves geridas pelo cliente (CMK) para encriptar os seus dados, pode utilizar a sua própria conta de armazenamento (finalidade geral V1 ou V2) para armazenar quaisquer ativos de dados privados que sejam exigidos pelo tempo de execução do Stream Analytics. A sua conta de armazenamento pode ser encriptada conforme necessário. Nenhum dos seus ativos de dados privados é armazenado permanentemente pela infraestrutura Stream Analytics. 

Esta definição deve ser configurada no momento da criação de emprego stream Analytics, e não pode ser modificada ao longo do ciclo de vida do trabalho. Não é recomendada a modificação ou eliminação do armazenamento que está a ser utilizado pelo seu Stream Analytics. Se eliminar a sua conta de armazenamento, eliminará permanentemente todos os ativos de dados privados, o que fará com que o seu trabalho falhe. 

Atualizar ou rodar chaves na sua conta de armazenamento não é possível utilizando o portal Stream Analytics. Pode atualizar as teclas utilizando as APIs REST.


## <a name="configure-storage-account-for-private-data"></a>Configure a conta de armazenamento para dados privados 

Utilize os seguintes passos para configurar a sua conta de armazenamento para ativos de dados privados. Esta configuração é feita a partir do seu trabalho de Stream Analytics, não da sua conta de armazenamento.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure. 

1. Selecione **o trabalho**  **analytics** >Stream Analytics na lista de resultados. 

1. Preencha a página de trabalho do Stream Analytics com detalhes necessários, como nome, região e escala. 

1. Selecione a caixa de verificação que diz *"Proteja todos os ativos de dados privados necessários por este trabalho na minha conta de Armazenamento".*

1. Selecione uma conta de armazenamento a partir da sua subscrição. Note que esta definição não pode ser modificada ao longo do ciclo de vida do trabalho. 

   ![Definições de conta de armazenamento de dados privados](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Ativos de dados privados que são armazenados

Quaisquer dados privados que sejam necessários para serem persistidos pelo Stream Analytics estão armazenados na sua conta de armazenamento. Exemplos de ativos de dados privados incluem: 

* Consultas que autorizou e respetivas configurações relacionadas  

* Funções definidas pelo utilizador 

* Pontos de verificação necessários pelo tempo de execução do Stream Analytics

* Instantâneos de dados de referência 

Os detalhes de ligação dos seus recursos, que são utilizados pelo seu trabalho stream analytics, também estão armazenados. Criptografe a sua conta de armazenamento para proteger todos os seus dados. 

Para ajudá-lo a cumprir as suas obrigações de conformidade em qualquer indústria ou ambiente regulado, pode ler mais sobre as ofertas de [conformidade da Microsoft.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 

## <a name="known-issues"></a>Problemas conhecidos
Existe um problema conhecido em que um trabalho que utiliza a chave gerida pelo cliente se depara com falhas ao utilizar identidade gerida para autenticar quaisquer inputs ou saídas. Está a ser trabalhada uma solução para esta questão e estará disponível num futuro próximo. 

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta de Armazenamento Azure](../storage/common/storage-account-create.md)
* [Compreender as inputs para azure stream analytics](stream-analytics-add-inputs.md)
* [Conceitos de checkpoint e repetição em empregos de Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Utilização de dados de referência para procuras no Stream Analytics](stream-analytics-use-reference-data.md)
