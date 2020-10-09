---
title: Soft Delete será ativado em todos os Cofres de Chaves Azure / Microsoft Docs
description: Utilize este documento para adotar uma eliminação suave para todos os cofres-chave.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: sudbalas
ms.openlocfilehash: 0e811cc219002c034afb968be760ce2c249b08f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825253"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>A eliminação suave será ativada em todos os cofres-chave

> [!WARNING]
> **Breaking Change**: A capacidade de excluir o soft-delete será depreciada até ao final do ano e a proteção para eliminação suave será automaticamente ligada para todos os cofres-chave.  Os utilizadores e administradores do Azure Key Vault devem permitir a eliminação suave dos cofres das chaves imediatamente.
>
> Para o HSM gerido, a eliminação suave é ativada por padrão e não pode ser desativada.

Quando um segredo é eliminado de um cofre sem proteção de eliminação suave, o segredo é permanentemente apagado. Os utilizadores podem atualmente optar por excluir soft-delete durante a criação do cofre de chaves, mas, para proteger os seus segredos de eliminação acidental ou maliciosa por um utilizador, a Microsoft irá em breve permitir a proteção de eliminação suave em **todos os** cofres-chave, e os utilizadores deixarão de ter a opção de excluir ou desligar.

:::image type="content" source="../media/softdeletediagram.png" alt-text=">de texto alt<":::

Para obter detalhes completos sobre a funcionalidade de eliminação suave, consulte [a visão geral do Azure Key Vault.](soft-delete-overview.md)

## <a name="how-do-i-respond-to-breaking-changes"></a>Como respondo a mudanças de rutura

Um objeto de cofre chave não pode ser criado com o mesmo nome que um objeto de cofre chave no estado de apagação suave.  Por exemplo, se eliminar uma chave nomeada `test key` no cofre A, não será capaz de criar uma nova chave chamada `test key` no cofre de chaves A até que o objeto apagado seja `test key` purgado.

### <a name="application-changes"></a>Alterações de aplicação

Se a sua aplicação assumir que a eliminação suave não está ativada e espera que os nomes de cofre secreto ou chave apagados estejam disponíveis para reutilização imediata, a sua lógica de aplicação terá de fazer as seguintes alterações para adotar esta alteração.

1. Apague o cofre de chaves original ou segredo
2. Purgue o cofre ou o segredo no estado de eliminação suave.
3. Aguarde – a recriação imediata pode resultar num conflito.
4. Re-crie o cofre com o mesmo nome.
5. Implementar novamente tentar se a operação de criação ainda resultar num erro de conflito de nomes, pode levar até 10 minutos para que os registos dns sejam atualizados no pior dos cenários.

### <a name="administration-changes"></a>Alterações administrativas

Os diretores de segurança que precisam de ter acesso a apagar permanentemente segredos devem ter permissão de política de acesso adicional para expurgar estes segredos e o cofre chave.

Se tiver uma Política Azure nos seus cofres-chave que determina que a eliminação suave seja desligada, esta política terá de ser desativada.  Poderá ter de agravar este problema a um administrador que controla as Políticas Azure aplicadas ao seu ambiente. Se esta política não for desativada, poderá perder a capacidade de criar novos cofres-chave no âmbito da política aplicada.

Se a sua organização estiver sujeita a requisitos legais de conformidade e não puder permitir que cofres e segredos apagados permaneçam em estado recuperável, por um longo período de tempo, terá de ajustar o período de retenção de soft-delete, que é configurável entre 7 a 90 dias, para cumprir os padrões da sua organização.

## <a name="procedures"></a>Procedimentos

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Audite os seus cofres-chave para verificar se a eliminação suave está ativada

1. Inicie sessão no portal do Azure.
2. Procure "Política Azure".
3. Selecione "Definições".
4. Na categoria, selecione "Key Vault" no filtro.
5. Selecione a política "Key Vault Objects Should Be Recoverable".
6. Clique em "Atribuir".
7. Desaça o âmbito da sua subscrição.
8. Selecione "Review + Create".
9. Dentro pode levar até 24 horas para uma varredura completa do seu ambiente para completar.
10. Na Lâmina de Política Azure, clique em "Conformidade".
11. Selecione a política que aplicou.

Deverá agora ser capaz de filtrar e ver qual dos seus cofres-chave tem uma eliminação suave ativada (recursos em conformidade) e quais as abóbadas-chave que não têm uma eliminação suave ativada (recursos não conformes).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Ligue soft Delete para um cofre de chaves existente

1. Inicie sessão no portal do Azure.
2. Procure o seu Cofre de Chaves.
3. Selecione "Propriedades" nas definições.
4. Em Soft-Delete, selecione o botão de rádio correspondente a "Ativar a recuperação deste cofre e dos seus objetos".
5. Desace o período de retenção para a eliminação suave.
6. Selecione "Save".

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Conceder permissões de política de acesso à purga a um diretor de segurança

1. Inicie sessão no portal do Azure.
2. Procure o seu Cofre de Chaves.
3. Selecione "Políticas de acesso" em definições.
4. Selecione o principal de serviço a que pretende conceder acesso.
5. Para cada entrega sob tecla, as permissões secretas e de certificados desloquem-se para "Operações Privilegiadas" e selecione a permissão "Purga".

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-this-change-affect-me"></a>Esta mudança afeta-me?

Se já tiver uma eliminação suave ligada ou se não eliminar e recriar objetos de cofre com o mesmo nome, provavelmente não notará qualquer alteração no comportamento do cofre da chave.

Se tiver uma aplicação que elimina e recria frequentemente objectos-chave com as mesmas convenções de nomeação, terá de fazer alterações na lógica da sua aplicação para manter o comportamento esperado. Por favor, veja o "Como respondo a mudanças de rutura?" secção acima.

### <a name="how-do-i-benefit-from-this-change"></a>Como é que me beneficio desta mudança?

A proteção de eliminação suave fornecerá à sua organização uma camada adicional de proteção contra a eliminação acidental ou maliciosa. Como administrador de cofre chave, pode restringir o acesso a permissões de recuperação e purga.

Se um utilizador eliminar acidentalmente um cofre ou segredo de uma chave, pode conceder-lhes permissões de acesso para recuperar o segredo por si mesmos sem criar o risco de que apaguem permanentemente o cofre secreto ou chave. Este processo de autosserviço minimizará o tempo de insusimento no seu ambiente e garantirá a disponibilidade dos seus segredos.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Como é que descundo se preciso de agir?

Siga os passos acima na secção intitulada "Procedimento para auditar os cofres das chaves para verificar se Soft-Delete está ligado". Qualquer cofre-chave que não tenha a eliminação suave ligada será afetado por esta alteração. Em breve, estarão disponíveis ferramentas adicionais para ajudar na auditoria e este documento será atualizado.

### <a name="what-action-do-i-need-to-take"></a>Que ação preciso de tomar?

Certifique-se de que não tem de fazer alterações na sua lógica de aplicação. Assim que tiver confirmado isso, ligue-se a todos os cofres das chaves. Isto garantirá que não será afetado por uma mudança de rutura quando a eliminação suave for ligada para todos os cofres chave no final do ano.

### <a name="by-when-do-i-need-to-take-action"></a>Quando é que preciso de agir?

A eliminação suave será ligada para todos os cofres chave até ao final do ano. Para se certificar de que as suas aplicações não são afetadas, ligue o mais rapidamente possível nos cofres das chaves.

## <a name="what-will-happen-if-i-dont-take-any-action"></a>O que vai acontecer se eu não tomar nenhuma ação?

Se não tomar nenhuma ação, a eliminação suave será automaticamente ligada para todos os seus cofres chave no final do ano. Isto pode resultar em erros de conflito se tentar eliminar um objeto de cofre e recriá-lo com o mesmo nome sem o purgar primeiro do estado apagado. Isto pode causar a falha das suas aplicações ou automação.

## <a name="next-steps"></a>Passos seguintes

- Contacte-nos com quaisquer questões relativas a esta alteração em [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Leia a [visão geral do Soft-delete](soft-delete-overview.md)
