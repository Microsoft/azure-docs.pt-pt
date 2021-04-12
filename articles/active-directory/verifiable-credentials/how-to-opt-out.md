---
title: Opte por não ter as credenciais verificáveis do Diretório Ativo Azure (Pré-visualização)
description: Saiba como excluir a pré-visualização de credenciais verificáveis
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: d6e72b6d6f566fcf3f52e1c48ab6824c0e9a968e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222797"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>Opte pelas credenciais verificáveis (Pré-visualização)

Neste artigo:

- A razão pela qual pode ter de optar por não o fazer.
- Os passos necessários.
- O que acontece com os seus dados?
- Efeito nas credenciais verificáveis existentes.

> [!IMPORTANT]
> Azure Ative Directory Verifiable Credentials está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

- Credenciais verificáveis completas a bordo.

## <a name="potential-reasons-for-opting-out"></a>Razões potenciais para excluir

Neste momento, não temos a capacidade de fazer modificações na informação de domínio. Como resultado, se cometer um erro ou decidir que quer fazer uma mudança, não há outra opção disponível além de optar e recomeçar.

## <a name="the-steps-required"></a>Os passos necessários

1. A partir do portal Azure procure credenciais verificáveis.
2. Escolha **Definições** no menu do lado esquerdo.
3. Na secção, **rejuste a sua organização,** selecione **Eliminar todas as credenciais e opte por não fazer a pré-visualização**.

   ![definições reset org](media/how-to-opt-out/settings-reset.png)

4. Leia a mensagem de aviso e continue a selecionar **Eliminar e excluir**.

   ![definições eliminar e excluir](media/how-to-opt-out/delete-and-opt-out.png)

Optou agora por não fazer a pré-visualização de credenciais verificáveis. Continua a ler para perceber o que se passa debaixo do capot.

## <a name="what-happens-to-your-data"></a>O que acontece com os seus dados?

Quando completar a exclusão do serviço de credenciais verificáveis do Diretório Ativo Azure, serão realizadas as seguintes ações:

- As teclas DID no Cofre de Chaves serão [apagadas suavemente.](../../key-vault/general/soft-delete-overview.md)
- O objeto emitente será eliminado da nossa base de dados.
- O identificador do inquilino será apagado da nossa base de dados. 
- Todos os contratos serão eliminados da nossa base de dados.

Uma vez que um opt-out ocorre, você não será capaz de recuperar o seu DID ou realizar quaisquer operações no seu DID. Este passo é uma operação unidireccionar, e você precisa optar novamente, o que resulta na criação de um novo DID.  

## <a name="effect-on-existing-verifiable-credentials"></a>Efeito sobre as credenciais verificáveis existentes

Todas as credenciais verificáveis já emitidas continuarão a existir. Não serão invalidados criptograficamente, uma vez que o seu DID permanecerá resolúvel através do ION.
No entanto, quando as partes que contam chamam o estatuto de API, receberão sempre uma mensagem de falha.

## <a name="next-steps"></a>Passos seguintes

- Confisca as credenciais verificáveis no seu [inquilino Azure](get-started-verifiable-credentials.md)