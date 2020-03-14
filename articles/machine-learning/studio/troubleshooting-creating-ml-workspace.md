---
title: Resolução de problemas num espaço de trabalho
titleSuffix: ML Studio (classic) - Azure
description: Este guia fornece soluções para alguns desafios frequentemente encontrados quando está a criar espaços de trabalho do Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ccd63e16382aca8e16eb67efba951a055eb254
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217827"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Guia de resolução de problemas: Criar e ligar-se a um estúdio de aprendizagem automática Azure (clássico) espaço de trabalho

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Este guia fornece soluções para alguns desafios frequentemente encontrados quando está a criar espaços de trabalho do Azure Machine Learning Studio (clássico).

## <a name="workspace-owner"></a>Proprietário do espaço de trabalho
Para abrir um espaço de trabalho no Machine Learning Studio (clássico), tem de ser inscrito na Conta Microsoft que usou para criar o espaço de trabalho, ou precisa de receber um convite do proprietário para se juntar ao espaço de trabalho. A partir do portal Azure pode gerir o espaço de trabalho, que inclui a capacidade de configurar o acesso.

Para obter mais informações sobre a gestão de um espaço de trabalho, consulte [Gerir um estúdio de aprendizagem automática Azure (clássico)].

[Gerir um estúdio de aprendizagem automática Azure (clássico)]: manage-workspace.md

## <a name="allowed-regions"></a>Regiões permitidas
O Machine Learning está atualmente disponível num número limitado de regiões. Se a sua subscrição não incluir uma destas regiões, poderá ver a mensagem de erro: "Não tem subscrições nas regiões permitidas"

Para solicitar que uma região seja adicionada à sua subscrição, crie um novo pedido de suporte da Microsoft a partir do portal Azure, escolha **a Faturação** como o tipo de problema e siga as instruções para submeter o seu pedido.

## <a name="storage-account"></a>Conta de armazenamento
O serviço de Aprendizagem automática precisa de uma conta de armazenamento para armazenar dados. Pode utilizar uma conta de armazenamento existente, ou pode criar uma nova conta de armazenamento quando criar o novo espaço de trabalho do Machine Learning Studio (clássico) (se tiver quota para criar uma nova conta de armazenamento).

Depois de criado o novo espaço de trabalho do Machine Learning Studio (clássico), pode iniciar sessão no Machine Learning Studio (clássico) utilizando a conta microsoft que usou para criar o espaço de trabalho. Se encontrar a mensagem de erro, "Workspace Not Found" (semelhante à seguinte imagem), utilize os seguintes passos para eliminar os cookies do seu navegador.

![Espaço de trabalho não encontrado](media/troubleshooting-creating-ml-workspace/screen3.png)

**Para eliminar os cookies do navegador**

1. Se utilizar o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **opções**de Internet .  

   ![Opções de Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Sob o separador **Geral,** clique em **Apagar...**

   ![Separador Geral](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Na caixa de diálogo 'Excluir Histórico de **Navegação',** certifique-se de que os **Cookies e os dados do site** são selecionados e clique em **Eliminar**.

   ![Eliminar cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

Depois de eliminados os cookies, reinicie o navegador e depois vá para a página [do Microsoft Azure Machine Learning Studio (clássico).](https://studio.azureml.net) Quando for solicitado um nome de utilizador e uma palavra-passe, introduza a mesma conta Microsoft que usou para criar o espaço de trabalho.

## <a name="comments"></a>Comentários

O nosso objetivo é tornar a experiência de Machine Learning o mais perfeita possível. Por favor, publique quaisquer comentários e problemas no [fórum azure machine learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para nos ajudar a servi-lo melhor.
