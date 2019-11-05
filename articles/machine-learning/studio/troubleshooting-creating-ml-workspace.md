---
title: Solucionando problemas de um espaço de trabalho
titleSuffix: Azure Machine Learning Studio (classic)
description: Este guia fornece soluções para alguns desafios frequentemente encontrados quando você está configurando a versão clássica dos espaços de trabalho do Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7d4aebf71ef16287a415e4c39eed1328c9359f6c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492595"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Guia de solução de problemas: criar e conectar-se a um espaço de trabalho Azure Machine Learning Studio (clássico)
Este guia fornece soluções para alguns desafios frequentemente encontrados quando você está configurando espaços de trabalho do Azure Machine Learning Studio (clássico).



## <a name="workspace-owner"></a>Proprietário do espaço de trabalho
Para abrir um espaço de trabalho no Machine Learning Studio (clássico), você deve estar conectado à conta da Microsoft usada para criar o espaço de trabalho ou você precisa receber um convite do proprietário para ingressar no espaço de trabalho. No portal do Azure você pode gerenciar o espaço de trabalho, que inclui a capacidade de configurar o acesso.

Para obter mais informações sobre como gerenciar um espaço de trabalho, consulte [gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)].

[Gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)]: manage-workspace.md

## <a name="allowed-regions"></a>Regiões permitidas
O Machine Learning está disponível atualmente em um número limitado de regiões. Se sua assinatura não incluir uma dessas regiões, você poderá ver a mensagem de erro "você não tem assinaturas nas regiões permitidas".

Para solicitar que uma região seja adicionada à sua assinatura, crie uma nova solicitação de suporte da Microsoft na portal do Azure, escolha **cobrança** como o tipo de problema e siga os prompts para enviar sua solicitação.

## <a name="storage-account"></a>Conta de armazenamento
O serviço de Machine Learning precisa de uma conta de armazenamento para armazenar dados. Você pode usar uma conta de armazenamento existente ou pode criar uma nova conta de armazenamento ao criar o novo espaço de trabalho Machine Learning Studio (clássico) (se você tiver uma cota para criar uma nova conta de armazenamento).

Depois que o novo espaço de trabalho de Machine Learning Studio (clássico) for criado, você poderá entrar no Machine Learning Studio (clássico) usando o conta Microsoft usado para criar o espaço de trabalho. Se você encontrar a mensagem de erro "espaço de trabalho não encontrado" (semelhante à captura de tela a seguir), use as etapas a seguir para excluir os cookies do navegador.

![Espaço de trabalho não encontrado](media/troubleshooting-creating-ml-workspace/screen3.png)

**Para excluir cookies do navegador**

1. Se você usar o Internet Explorer, clique no botão **ferramentas** no canto superior direito e selecione **Opções da Internet**.  

   ![Opções da Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Na guia **geral** , clique em **excluir...**

   ![Guia geral](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Na caixa de diálogo **Excluir Histórico de navegação** , verifique se **cookies e dados do site** estão selecionados e clique em **excluir**.

   ![Excluir cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

Depois que os cookies forem excluídos, reinicie o navegador e vá para a página [Microsoft Azure Machine Learning Studio (clássica)](https://studio.azureml.net) . Quando for solicitado um nome de usuário e uma senha, insira o mesmo conta Microsoft usado para criar o espaço de trabalho.

## <a name="comments"></a>Comentários

Nosso objetivo é tornar a experiência de Machine Learning o mais simples possível. Poste comentários e problemas no [Fórum de Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para nos ajudar a atendê-lo melhor.
