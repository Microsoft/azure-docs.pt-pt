---
title: Azure AD Connect cloud sync acidental elimina
description: Este tópico descreve como utilizar a função de eliminação acidental para evitar supressões.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785190"
---
# <a name="accidental-delete-prevention"></a>Prevenção acidental de eliminação

O documento que se segue descreve a função de eliminação acidental para a sincronização da nuvem Azure AD Connect.  A função de eliminação acidental foi concebida para o proteger de alterações de configuração acidentais e alterações no diretório no local que afetariam muitos utilizadores e grupos.  Esta funcionalidade permite-lhe:

- Configure a capacidade de evitar eliminações acidentais automaticamente. 
- Desa ajuste o # de objetos (limiar) para além do qual a configuração irá produzir efeito 
- criar um endereço de e-mail de notificação para que possam receber uma notificação por e-mail uma vez que o trabalho de sincronização em questão é colocado em quarentena para este cenário 

Para utilizar esta função, estabeleceu o limiar para o número de objetos que, se eliminados, a sincronização deve parar.  Assim, se este número for alcançado, a sincronização irá parar e uma notificação será enviada para o e-mail especificado.  Esta notificação permitir-lhe-á investigar o que se passa.


## <a name="configure-accidental-delete-prevention"></a>Configure a prevenção acidental de eliminação acidental
Para utilizar a nova funcionalidade, siga os passos abaixo.


1.  No portal do Azure, selecione **Azure Active Directory**.
2.  Selecione **Azure Ad Connect**.
3.  **Selecione Gerir a sincronização de nuvens**.
4. Em **Configuração,** selecione a sua configuração.
5. Em Definições preenchem os **seguintes:**
    - **Email de notificação** - e-mail usado para notificações
    - **Prevenir supressões acidentais** - verifique esta caixa para ativar a funcionalidade
    - **Limiar de eliminação acidental** - insira o número de objetos para parar a sincronização e enviar uma notificação

![Eliminações acidentais](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Recuperação de uma instância de eliminação acidental
Se encontrar uma eliminação acidental, verá isto no estado da configuração do seu agente de provisionamento.  Dirá que **eliminar limiar excedeu**.
 
![Estado de eliminação acidental](media/how-to-accidental-deletes/delete-1.png)

Ao clicar no **limiar de Eliminar excedido,** verá a informação do estado de sincronização.  Isto fornecerá detalhes adicionais. 
 
 ![Estado de sincronização](media/how-to-accidental-deletes/delete-2.png)

Ao clicar à direita nas elipses, terá as seguintes opções:
 - Ver registo de provisionamento
 - Ver agente
 - Permitir eliminações

 ![Clicar com o botão direito do rato](media/how-to-accidental-deletes/delete-3.png)

Utilizando **o registo de provisões View,** pode ver as entradas do **StagedDelete** e rever as informações fornecidas nos utilizadores que foram eliminados.
 
 ![Registos de aprovisionamento](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Permitindo eliminações

A ação **de eliminação de permitir** eliminará os objetos que desencadearam o limiar de eliminação acidental.  Utilize o seguinte procedimento para aceitar as eliminações.  

1. Clique com o botão direito nas elipses e selecione **Permitir eliminações**.
2. Clique em **Sim** na confirmação para permitir as supressões.
 
 ![Sim, na confirmação](media/how-to-accidental-deletes/delete-4.png)

3. Verá a confirmação de que as supressões foram aceites e que o estatuto voltará a saudável com o próximo ciclo. 
 
 ![Aceitar eliminações](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Rejeitar exclusões

Se não quiser permitir as supressões, tem de fazer o seguinte:
- investigar a origem das supressões
- corrigir o problema (exemplo, u foi movido para fora do âmbito acidentalmente e agora voltou a adicioná-lo ao âmbito de aplicação)
- Executar **Reiniciar sincronização** na configuração do agente

## <a name="next-steps"></a>Passos seguintes 

- [Azure AD Connect cloud sync resolução de problemas?](how-to-troubleshoot.md)
- [Códigos de erro de sincronização de nuvem Azure AD Connect](reference-error-codes.md)
 

