---
title: Configure servidor MFA - Diretório Ativo Azure
description: Saiba como configurar definições para O Servidor MFA Azure no portal Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53763e62e8d7aa5b77ae451ef6427c1e09bff9f0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742702"
---
# <a name="configure-mfa-server-settings"></a>Configurar as definições do Servidor MFA

Este artigo ajuda-o a gerir as definições do Servidor Azure MFA no portal Azure.

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o MFA Server para novas implementações. Os novos clientes que gostariam de exigir a autenticação de vários fatores dos seus utilizadores devem utilizar a autenticação multi-factor Azure AD baseada na nuvem. Os clientes existentes que tenham ativado o MFA Server antes de 1 de julho poderão descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

Estão disponíveis as seguintes definições do MFA Server:

| Funcionalidade | Descrição |
| ------- | ----------- |
| Definições do servidor | Descarregue o MFA Server e gere credenciais de ativação para inicializar o seu ambiente |
| [Bypass único](#one-time-bypass) | Permitir que um utilizador autentica sem realizar a autenticação de vários fatores por um tempo limitado. |
| [Regras de caching](#caching-rules) |  O caching é usado principalmente quando sistemas no local, como VPN, enviam múltiplos pedidos de verificação enquanto o primeiro pedido ainda está em curso. Esta funcionalidade permite que os pedidos subsequentes tenham sucesso automaticamente, depois de o utilizador ter sucesso na primeira verificação em curso. |
| Estado do servidor | Consulte o estado dos seus servidores MFA no local, incluindo versão, estado, IP e última hora e data de comunicação. |

## <a name="one-time-bypass"></a>Bypass único

A funcionalidade de bypass único permite ao utilizador autenticar uma única vez sem realizar a autenticação de vários fatores. O bypass é temporário e expira após um número especificado de segundos. Em situações em que a aplicação móvel ou o telemóvel não esteja a receber uma notificação ou chamada telefónica, pode permitir um bypass único para que o utilizador possa aceder ao recurso pretendido.

Para criar um bypass único, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
1. Procure e selecione **O Diretório Ativo Azure,** em seguida, navegue pelo bypass de **segurança**  >  **MFA**  >  **uma vez**.
1. Selecione **Adicionar**.
1. Se necessário, selecione o grupo de replicação para o bypass.
1. Introduza o nome de utilizador como `username\@domain.com` . Introduza o número de segundos que o bypass deve durar e o motivo do bypass.
1. Selecione **Adicionar**. O prazo entra em vigor imediatamente. O utilizador tem de fazer o seu sedudo antes de expirar o bypass único.

Também pode ver o relatório de bypass único a partir desta mesma janela.

## <a name="caching-rules"></a>Regras de caching

Pode definir um período de tempo para permitir tentativas de autenticação após a autenticação de um utilizador utilizando _a função de caching._ As tentativas de autenticação subsequentes para o utilizador dentro do período de tempo especificado sucedem-se automaticamente.

O caching é usado principalmente quando sistemas no local, como VPN, enviam múltiplos pedidos de verificação enquanto o primeiro pedido ainda está em curso. Esta funcionalidade permite que os pedidos subsequentes tenham sucesso automaticamente, depois de o utilizador ter sucesso na primeira verificação em curso.

>[!NOTE]
> A função de caching não se destina a ser utilizada para iniciar sing-ins no Azure Ative Directory (Azure AD).

Para configurar o caching, complete os seguintes passos:

1. Consulte as regras **de**  >  **Security**  >  **MFA**  >  **Caching** MFA de Segurança do Diretório Ativo Azure .
1. Selecione **Adicionar**.
1. Selecione o **tipo de cache** da lista de drop-down. Introduza o número máximo de **segundos** de cache .
1. Se necessário, selecione um tipo de autenticação e especifique uma aplicação.
1. Selecione **Adicionar**.

## <a name="next-steps"></a>Passos seguintes

Estão disponíveis opções adicionais de configuração do MFA Server a partir da consola web do próprio MFA Server. Também pode [configurar o Azure MFA Server para uma elevada disponibilidade.](howto-mfaserver-deploy-ha.md)
