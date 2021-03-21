---
title: Ativar a eliminação suave em todos os objetos do cofre chave - Azure Key Vault | Microsoft Docs
description: Utilize este documento para adotar uma eliminação suave para todos os cofres-chave e para fazer alterações de aplicação e administração para evitar erros de conflito.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: b96f2ca4f925846bd252e5cfd35088d832f5c216
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572872"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>A eliminação suave será ativada em todos os cofres-chave

> [!WARNING]
> Mudança de rutura: a capacidade de excluir o soft-delete será depreciada em breve. Os utilizadores e administradores do Azure Key Vault devem permitir a eliminação suave dos cofres das chaves imediatamente.
>
> Para o Azure Key Vault Gerido HSM, a eliminação suave é ativada por padrão e não pode ser desativada.

Quando um segredo é eliminado de um cofre sem proteção de eliminação suave, o segredo é permanentemente apagado. Os utilizadores podem atualmente optar por não eliminar suavemente durante a criação do cofre de chaves. No entanto, a Microsoft em breve permitirá a proteção de eliminação suave em todos os cofres-chave para proteger os segredos de eliminação acidental ou maliciosa por parte de um utilizador. Os utilizadores deixarão de poder excluir ou desligar o soft-delete.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Diagrama que mostra como um cofre de teclas é eliminado com proteção de eliminação suave versus sem proteção de eliminação suave.":::

Para obter detalhes completos sobre a funcionalidade de eliminação suave, consulte [a visão geral do Azure Key Vault.](soft-delete-overview.md)

## <a name="can-my-application-work-with-soft-delete-enabled"></a>A minha aplicação pode funcionar com soft-delete ativado?

> [!Important] 
> Reveja cuidadosamente as seguintes informações antes de ligar o soft-delete para os seus cofres chave.

Os nomes do cofre são globalmente únicos. Os nomes dos segredos guardados num cofre também são únicos. Não poderá reutilizar o nome de um cofre ou chave que existe no estado de apagação suave. 

Por exemplo, se a sua aplicação criar programáticamente um cofre de chaves chamado "Cofre A" e mais tarde eliminar "Cofre A", o cofre-chave será movido para o estado de eliminação suave. A sua aplicação não será capaz de recriar outro cofre chamado "Cofre A" até que o cofre da chave seja purgado do estado de apagamento suave. 

Além disso, se a sua aplicação criar uma chave chamada `test key` "Vault A" e posteriormente eliminar essa chave, a sua aplicação não será capaz de criar uma nova chave chamada `test key` "Cofre A" até que o `test key` objeto seja purgado do estado de eliminação suave. 

Tentar eliminar um objeto de cofre chave e reu criá-lo com o mesmo nome sem o purgar do estado apagado suave primeiro pode causar erros de conflito. Estes erros podem causar falhas nas suas aplicações ou na automatização. Consulte a sua equipa dev antes de fazer as seguintes alterações de aplicação e administração. 

### <a name="application-changes"></a>Alterações de aplicação

Se a sua aplicação assumir que a eliminação suave não está ativada e espera que os nomes de cofre secretos ou chave apagados estejam disponíveis para reutilização imediata, terá de fazer as seguintes alterações na lógica da sua aplicação.

1. Apague o cofre original ou segredo.
1. Purgue o cofre ou o segredo no estado de eliminação suave.
1. Espere que a purga termine. A recriação imediata pode resultar num conflito.
1. Re-crie o cofre com o mesmo nome.
1. Se a operação de criação ainda resultar num erro de conflito de nomes, tente recriar novamente o cofre da chave. Os registos do Azure DNS podem demorar até 10 minutos a ser atualizados no pior dos cenários.

### <a name="administration-changes"></a>Alterações administrativas

Os diretores de segurança que precisam de ter acesso a apagar permanentemente segredos devem ter mais permissões de política de acesso para expurgar estes segredos e o cofre chave.

Desative qualquer política de Azure nos seus cofres-chave que determina que a eliminação suave seja desligada. Pode ter de agravar esta questão a um administrador que controla as políticas da Azure aplicadas ao seu ambiente. Se esta política não for desativada, pode perder a capacidade de criar novos cofres chave no âmbito da política aplicada.

Se a sua organização estiver sujeita a requisitos legais de conformidade e não puder permitir que cofres e segredos apagados permaneçam em estado recuperável por um longo período de tempo, terá de ajustar o período de retenção de soft-delete para cumprir os padrões da sua organização. Pode configurar o período de retenção para durar de 7 a 90 dias.

## <a name="procedures"></a>Procedimentos

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Audite os seus cofres-chave para verificar se a eliminação suave está ativada

1. Inicie sessão no portal do Azure.
1. Procurar a **Política Azure**.
1. Selecione **Definições**.
1. Na **categoria**, selecione **Key Vault** no filtro.
1. Selecione o Cofre de Chaves deve ter uma política **ativada para eliminar suavemente.**
1. Selecione **Atribuir**.
1. Desaça o âmbito da sua subscrição.
1. Certifique-se de que o efeito da apólice está definido para **auditoria.**
1. Selecione **Rever + Criar**. Uma varredura completa do seu ambiente pode levar até 24 horas para ser completado.
1. No painel **de política Azure,** selecione **Compliance**.
1. Selecione a política que aplicou.

Agora pode filtrar e ver quais as abóbadas-chave que têm uma eliminação suave ativada (recursos compatíveis) e quais as abóbadas-chave que não têm a eliminação suave ativada (recursos não conformes).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Ligue o soft-delete para um cofre de chaves existente

1. Inicie sessão no portal do Azure.
1. Procure o cofre da chave.
1. Selecione **Propriedades** em **Definições**.
1. Em **Soft-Delete,** selecione a **recuperação ativa deste cofre e a opção de objetos.**
1. Desace o período de retenção para a eliminação suave.
1. Selecione **Guardar**.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Conceder permissões de política de acesso à purga a um diretor de segurança

1. Inicie sessão no portal do Azure.
1. Procure o cofre da chave.
1. Selecione **Políticas de Acesso** em **Definições**.
1. Selecione o principal de serviço a que pretende conceder acesso.
1. Mova-se através de cada menu suspenso sob as permissões **de Key,** **Secret** e Certificate até ver **Operações Privilegiadas.**  Selecione a permissão **de purga.**

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-this-change-affect-me"></a>Esta mudança afeta-me?

Se já tiver uma eliminação suave ligada ou se não apagar e recriar objetos de cofre com o mesmo nome, provavelmente não notará qualquer alteração no comportamento do cofre da chave.

Se tiver uma aplicação que elimina e recria objetos-chave com as mesmas convenções de nomeação frequentemente, terá de fazer alterações na lógica da sua aplicação para manter o comportamento esperado. Consulte a secção [de alterações de aplicação](#application-changes) neste artigo.

### <a name="how-do-i-benefit-from-this-change"></a>Como é que me beneficio desta mudança?

A proteção de eliminação suave proporciona à sua organização outra camada de proteção contra a eliminação acidental ou maliciosa. Como administrador de cofre chave, pode restringir o acesso a permissões de recuperação e purga.

Se um utilizador eliminar acidentalmente um cofre ou segredo de uma chave, pode conceder-lhes permissões de acesso para recuperar o segredo em si mesmos sem criar o risco de que eles apaguem permanentemente o cofre secreto ou chave. Este processo de autosserviço minimizará o tempo de inatividade no seu ambiente e garantirá a disponibilidade dos seus segredos.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Como é que descundo se preciso de agir?

Siga os passos na Auditoria os [seus cofres-chave para verificar se a secção de exclusão suave está ativada](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) neste artigo. Esta alteração afetará qualquer cofre chave que não tenha a eliminação suave ligada.

### <a name="what-action-do-i-need-to-take"></a>Que ação preciso de tomar?

Depois de ter confirmado que não precisa de fazer alterações na lógica da aplicação, ligue-se a todos os seus cofres chave.

### <a name="when-do-i-need-to-take-action"></a>Quando é que preciso de agir?

Para se certificar de que as suas aplicações não são afetadas, ligue o mais rapidamente possível nos cofres das chaves.

## <a name="next-steps"></a>Passos seguintes

- Contacte-nos com quaisquer questões sobre esta mudança em [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Leia a [visão geral de eliminação suave](soft-delete-overview.md).
