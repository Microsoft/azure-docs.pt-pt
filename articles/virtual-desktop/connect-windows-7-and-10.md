---
title: Ligar a pré-visualização de área de trabalho virtuais Windows a partir do Windows 10 ou Windows 7 - Azure
description: Como ligar à pré-visualização de ambiente de Trabalho Virtual do Windows a partir do Windows 10 ou Windows 7.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: b7d7b25d0355f2379b90313f17e2b595234df827
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145993"
---
# <a name="connect-from-windows-10-or-windows-7"></a>Ligar a partir do Windows 10 ou Windows 7

> Aplica-se a O Windows 7 e Windows 10.

Está disponível um cliente disponível para download que fornece acesso a recursos da demonstração de ambiente de Trabalho Virtual do Windows dos dispositivos que executam o Windows 7 e Windows 10.

> [!IMPORTANT]
> Não utilize **RemoteApp e ligações de ambiente de trabalho (RADC)** ou **ligação de ambiente de trabalho remoto (MSTSC)** para aceder aos recursos de área de Trabalho Virtual do Windows, porque a área de Trabalho Virtual do Windows não oferece suporte a qualquer cliente.

## <a name="install-the-client"></a>Instalar o cliente

[Baixe](https://go.microsoft.com/fwlink/?linkid=2068602) e instalar o cliente ao seu PC local. A instalação requer direitos de administrador.

## <a name="subscribe-to-a-feed"></a>Subscrever um feed

Obter a lista de recursos gerenciados disponíveis para si ao subscrever o feed fornecido pelo seu administrador. Subscrever torna os recursos disponíveis no seu PC local.

Para subscrever um feed:

1. Iniciar o cliente da lista de todas as aplicações, procure para **ambiente de trabalho remoto**.
1. Selecione **Subscribe** na página principal para se ligar ao serviço e recuperar os seus recursos.
1. **Inicie sessão no** com a sua conta de utilizador quando lhe for pedido.

Após a autenticação bem-sucedida, agora deve ver uma lista de recursos disponíveis para.

Pode iniciar recursos por um dos dois métodos.

- Na página de principal do cliente, faça duplo clique num recurso para iniciá-lo.
- Inicie um recurso, tal como faria normalmente de outras aplicações do Menu Iniciar.
  - Também pode procurar as aplicações na barra de pesquisa.

Depois de subscrever um feed, o conteúdo do feed é atualizado automaticamente em intervalos regulares. Podem ser adição, alteração ou removidos com base nas alterações feitas pelo seu administrador de recursos.

## <a name="view-the-details-of-a-feed"></a>Ver os detalhes de um feed

Depois de subscrever, pode ver informações adicionais sobre o feed acessando o painel de detalhes.

1. Na página de principal do cliente, selecione as reticências (**...** ) à direita do nome do feed.
1. No menu pendente, selecione **detalhes**.
1. O painel de detalhes é apresentada no lado direito do cliente.

O painel de detalhes contém informações úteis sobre o feed:

- O URL e o nome de utilizador utilizada para subscrever
- O número de aplicações e áreas de trabalho
- A data/hora da última atualização
- O estado da última atualização

Se for necessário, pode iniciar uma atualização manual selecionando na **atualizar agora**.

## <a name="unsubscribe-from-a-feed"></a>Anular a subscrição de um feed

Esta secção irá ensiná-lo como anular a subscrição de um feed. Pode anular a subscrição para subscrever novamente com uma conta diferente ou remover os recursos do sistema.

1. Na página de principal do cliente, selecione as reticências (**...** ) à direita do nome do feed.
1. No menu pendente, selecione **Unsubscribe**.
1. Reveja e selecione **continuar** na caixa de diálogo.

## <a name="update-the-client"></a>Atualizar o cliente

Quando uma nova versão do cliente estiver disponível, será notificado pelo cliente e o Centro de ação do Windows. Selecione a notificação para iniciar o processo de atualização.
