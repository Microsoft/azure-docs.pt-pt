---
title: Criar um laboratório no Azure DevTest Labs | Microsoft Docs
description: Este artigo acompanha-o através do processo de criação de um laboratório usando o portal Azure e a Azure DevTest Labs.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 962997bcc66188c66fd9db856fe44e4926f8e70c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019652"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Criar um laboratório no Azure DevTest Labs

Um laboratório no Azure DevTest Labs é a infraestrutura que abrange um grupo de recursos, como máquinas virtuais (VM), que lhe permite gerir melhor esses recursos através da especificação de limites e quotas. Este artigo serve de orientação durante o processo de criação de laboratórios com o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para criar um laboratório, precisa de:

* Uma subscrição do Azure. Para obter informações sobre opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/). Tem de ser o proprietário da subscrição para criar o laboratório.

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>Comece a utilizar o Azure DevTest Labs em minutos

Ao clicar no seguinte link, você será transferido para a página do portal Azure que lhe permite começar a criar um novo laboratório em Azure DevTest Labs.

[Comece a utilizar o Azure DevTest Labs em minutos](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>Preencha as definições para a sua nova conta

Na página **Criar um DevTest Labs,** preencha as seguintes definições.

> [!TIP]
> Na parte inferior de cada página, encontrará um link que lhe permite **descarregar um modelo para automação.**

### <a name="basic-settings"></a>Definições básicas

Por predefinição, vê o separador **Definições Básicas.** Preencha estes valores:

|Nome|Descrição|
|---|---|
|**Subscrição** | Obrigatório. Selecione a **Subscrição** para associar ao laboratório.|
|**Grupo de recursos**| Obrigatório. Insira um **nome para o grupo de recursos** para o laboratório. Criar um novo se não existir.|
|**Nome do laboratório**| Obrigatório. Insira um **nome** para o laboratório.|
|**Localização**|Obrigatório. Selecione um local para guardar o laboratório.|
|**Ambientes públicos**| Consulte [configurar e utilizar ambientes públicos.](devtest-lab-configure-use-public-environments.md)

### <a name="auto-shutdown-settings"></a>Definições de paragem automática

Mude para a página **de encerramento automático** para ver as suas definições. O encerramento automático permite desligar automaticamente todas as máquinas de um laboratório a uma hora programada todos os dias.

Na página, pode ativar o **encerramento automático** e definir os parâmetros para o desligamento automático de todos os VMs do laboratório. A funcionalidade de encerramento automático é principalmente uma funcionalidade de poupança de custos, na qual poderá especificar quando deseja que a VM seja automaticamente desligada. Pode alterar as definições de paragem automática após a criação do laboratório seguindo os passos descritos no artigo [Gerir todas as políticas para um laboratório em Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).

### <a name="networking"></a>Redes

Ao criar um laboratório, será criada uma rede padrão para si.

Mude para o **separador De Rede** para alterar/configurar a definição conforme quiser. Por exemplo, selecione uma rede virtual existente.

### <a name="tags"></a>Etiquetas

Introduza as informações de **NOME** e **VALOR** nas **Etiquetas** se quiser criar etiquetas personalizadas que são adicionadas a todos os recursos que criar no laboratório. As etiquetas são úteis para ajudar a gerir e organizar os recursos do laboratório por categoria. Para obter mais informações sobre etiquetas, incluindo como adicioná-las depois de criar o laboratório, veja [Add tags to a lab](devtest-lab-add-tag.md) (Adicionar etiquetas a um laboratório).

### <a name="review-and-create"></a>Rever e criar

Uma vez feito, **selecione Criar**. Pode monitorizar o estado do processo de criação de laboratório observando a área **de Notificações** no topo direito da página do portal. 

## <a name="completed-the-creation"></a>Concluída a criação

Uma vez concluído, o botão **Go para o recurso** aparece na parte inferior da página e na janela de notificação. Em alternativa, refresque a página **da DevTest Labs** para ver o laboratório recém-criado na lista de laboratórios.  

Pressione vá para o botão **de recursos** e será levado para a página inicial da sua nova conta DevTest Labs.

Também pode pesquisar por **DevTest Labs** no portal Azure. Selecione a sua nova conta na lista e entre na página inicial. 

## <a name="next-steps"></a>Passos seguintes

Assim que tiver criado o seu laboratório, seguem-se alguns passos a ter em consideração:

* [Acesso seguro a um laboratório](devtest-lab-add-devtest-user.md)
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md)
* [Criar um modelo de laboratório](devtest-lab-create-template.md)
* [Crie artefactos personalizados para os seus VMs](devtest-lab-artifact-author.md)
* [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)

