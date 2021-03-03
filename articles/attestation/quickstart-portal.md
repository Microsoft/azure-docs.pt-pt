---
title: 'Quickstart: Configurar a Azure Attestation utilizando o portal Azure'
description: Neste arranque rápido, você vai aprender a configurar e configurar um provedor de atestado usando o portal Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d1310e3c4b4a56a27219cce613e8f6109d32c8c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729401"
---
# <a name="quickstart-set-up-azure-attestation-by-using-the-azure-portal"></a>Quickstart: Configurar a Azure Attestation utilizando o portal Azure

Siga este quickstart para começar com a Azure Attestation. Saiba como gerir um fornecedor de atestado, um signatário de políticas e uma política utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="attestation-provider"></a>Provedor de atestado

Nesta secção, você vai criar um provedor de atestado e configurá-lo com políticas não assinadas ou políticas assinadas. Também aprenderá a visualizar e eliminar o fornecedor de atestado.

### <a name="create-and-configure-the-provider-with-unsigned-policies"></a>Criar e configurar o fornecedor com políticas não assinadas

1. Aceda ao menu do portal Azure ou à página inicial e selecione **Criar um recurso.**
1. Na caixa de pesquisa, insira **o atestado**.
1. Na lista de resultados, selecione **Microsoft Azure Attestation**.
1. Na página microsoft **Azure Attestation,** selecione **Criar**.
1. Na página do **provedor de atestado Create,** forneça as seguintes entradas:

   - **Subscrição**: selecione uma subscrição.
   - **Grupo de Recursos**: Selecione um grupo de recursos existente ou selecione **Criar novo** e introduza um nome de grupo de recursos.
   - **Nome**: Introduza um nome único.
   - **Localização**: Escolha uma localização.
   - **Ficheiro de certificados de sinalização de** política : Não faça o upload do ficheiro de certificados de sinalização de apólice para configurar o fornecedor com políticas não assinadas.

1. Depois de fornecer as entradas necessárias, selecione **Review+Create**.
1. Se houver problemas de validação, corrija-os e, em seguida, **selecione Criar**.

### <a name="create-and-configure-the-provider-with-signed-policies"></a>Criar e configurar o fornecedor com políticas assinadas

1. Aceda ao menu do portal Azure ou à página inicial e selecione **Criar um recurso.**
1. Na caixa de pesquisa, insira **o atestado**.
1. Na lista de resultados, selecione **Microsoft Azure Attestation**.
1. Na página microsoft **Azure Attestation,** selecione **Criar**.
1. Na página do **provedor de atestado Create,** forneça as seguintes informações:

   - **Subscrição**: selecione uma subscrição.
   - **Grupo de Recursos**: Selecione um grupo de recursos existente ou selecione **Criar novo** e introduza um nome de grupo de recursos.
   - **Nome**: Introduza um nome único.
   - **Localização**: Escolha uma localização.
   - **Ficheiro de certificados de sinalização de** política : Carregumente o ficheiro de certificados do signatário da apólice para configurar o fornecedor de atestado com políticas assinadas. [Consulte exemplos de certificados de sinalizadores de política](./policy-signer-examples.md).

1. Depois de fornecer as entradas necessárias, selecione **Review+Create**.
1. Se houver problemas de validação, corrija-os e, em seguida, **selecione Criar**.

### <a name="view-the-attestation-provider"></a>Ver o provedor de atestado

1. Aceda ao menu do portal Azure ou à página inicial e selecione **Todos os recursos**.
1. Na caixa de filtro, insira o nome do provedor de atestado e selecione-o.

### <a name="delete-the-attestation-provider"></a>Eliminar o fornecedor de atestado

Há duas formas de eliminar o fornecedor de atestado. Pode:

1. Aceda ao menu do portal Azure ou à página inicial e selecione **Todos os recursos**.
1. Na caixa de filtro, insira o nome do provedor de atestado.
1. Selecione a caixa de verificação e **selecione Delete**.
1. Introduza **sim** e selecione **Eliminar**.

Ou pode:

1. Aceda ao menu do portal Azure ou à página inicial e selecione **Todos os recursos**.
1. Na caixa de filtro, insira o nome do provedor de atestado.
1. Selecione o provedor de atestado e vá para a página geral.
1. Selecione **Eliminar** na barra de menus e selecione **Sim**.

## <a name="attestation-policy-signers"></a>Signatários da política de atestado

Siga os passos desta secção para visualizar, adicionar e eliminar certificados de sinalizadores de política.

### <a name="view-the-policy-signer-certificates"></a>Ver os certificados de sinalização de política

1. Aceda ao menu do portal Azure ou à página inicial e selecione **Todos os recursos**.
1. Na caixa de filtro, insira o nome do provedor de atestado.
1. Selecione o provedor de atestado e vá para a página geral.
1. Selecione **os certificados de sinalização** de política no menu de recursos no lado esquerdo da janela ou no painel inferior. Consulte um pedido para selecionar certificado para autenticação. Por favor, escolha a opção apropriada para prosseguir.
1. Selecione **Os certificados de sinalização de política de descarregamento**. O botão será desativado para os fornecedores de atestados criados sem o requisito de assinatura de políticas.
1. O ficheiro de texto descarregado terá todos os certificados em formato JWS.
1. Verifique a contagem de certificados e os certificados descarregados.

### <a name="add-the-policy-signer-certificate"></a>Adicione o certificado de sinalizador de apólice

1.  Aceda ao menu do portal Azure ou à página inicial e selecione **Todos os recursos**.
1.  Na caixa de filtro, insira o nome do provedor de atestado.
1.  Selecione o provedor de atestado e vá para a página geral.
1.  Selecione **os certificados de sinalização** de política no menu de recursos no lado esquerdo da janela ou no painel inferior.
1.  **Selecione Adicione** no menu superior. O botão será desativado para os fornecedores de atestados criados sem o requisito de assinatura de políticas.
1.  Faça o upload do ficheiro de certificado do signatário da apólice e selecione **Adicionar**. [Consulte exemplos de certificados de sinalizadores de política](./policy-signer-examples.md).

### <a name="delete-the-policy-signer-certificates"></a>Eliminar os certificados de sinalizador de apólice

1.  Aceda ao menu do portal Azure ou à página inicial e selecione **Todos os recursos**.
1.  Na caixa de filtro, insira o nome do provedor de atestado.
1.  Selecione o provedor de atestado e vá para a página geral.
1.  Selecione **os certificados de sinalização** de política no menu de recursos no lado esquerdo da janela ou no painel inferior.
1.  **Selecione Excluir** no menu superior. O botão será desativado para os fornecedores de atestados criados sem o requisito de assinatura de políticas.
1.  Faça o upload do ficheiro de certificado do signatário da política e selecione **Delete**. [Consulte exemplos de certificados de sinalizadores de política](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Política de atestado

Esta secção descreve como ver uma política de atestado e como configurar políticas que foram criadas com e sem um requisito de assinatura de políticas.

### <a name="view-an-attestation-policy"></a>Ver uma política de atestado

1.  Aceda ao menu do portal Azure ou à página inicial e selecione **Todos os recursos**.
1.  Na caixa de filtro, insira o nome do provedor de atestado.
1.  Selecione o provedor de atestado e vá para a página geral.
1.  Selecione **Política** no menu de recursos no lado esquerdo da janela ou no painel inferior. Consulte um pedido para selecionar certificado para autenticação. Por favor, escolha a opção apropriada para prosseguir.
1.  Selecione o **Tipo de Attestation** preferido e veja a **política atual**.

### <a name="configure-an-attestation-policy"></a>Configure uma política de atestado

Siga estes passos para fazer o upload de uma política em JWT ou formato de texto se o fornecedor de atestado foi criado sem um requisito de assinatura de política.

1. Aceda ao menu do portal Azure ou à página inicial e selecione **Todos os recursos**.
1. Na caixa de filtro, insira o nome do provedor de atestado.
1. Selecione o provedor de atestado e vá para a página geral.
1. Selecione **Política** no menu de recursos no lado esquerdo da janela ou no painel inferior.
1. **Selecione Configurar** no menu superior.
1. Selecione **o Formato de Política** como **JWT** ou como **Texto**.

   Se o fornecedor de atestado foi criado sem o requisito de assinatura de políticas, o utilizador pode carregar uma política em formato **JWT** ou **Text.**

      - Se escolheu o formato JWT, carrehe o ficheiro de política com o conteúdo da política no formato **JWT não assinado/assinado** e selecione **Save**. [Veja exemplos de política.](./policy-examples.md)
      - Se escolheu o formato de texto, faça o upload do ficheiro de política com o conteúdo em formato **Texto** ou introduza o conteúdo da política na área de texto e selecione **Guardar**. [Veja exemplos de política.](./policy-examples.md)

   Para a opção de upload de ficheiros, a pré-visualização da política é mostrada no formato de texto e não é editável.

1. Selecione **Refresh** no menu superior para ver a política configurada.


Se o fornecedor de atestado foi criado com um requisito de assinatura de política, siga estes passos para carregar uma política em formato JWT.

1.  Aceda ao menu do portal Azure ou à página inicial e selecione **Todos os recursos**.
1.  Na caixa de filtro, insira o nome do provedor de atestado.
1.  Selecione o provedor de atestado e vá para a página geral.
1.  Selecione **Política** no menu de recursos no lado esquerdo da janela ou no painel inferior.
1.  **Selecione Configurar** no menu superior.
1.  Faça o upload do ficheiro de política no **formato JWT assinado** e selecione **Save**. [Veja exemplos de política.](./policy-examples.md)

    Se o prestador de atestado tiver sido criado com um requisito de assinatura de política, o utilizador só pode carregar uma apólice no **formato JWT assinado**.

    Para a opção de upload de ficheiros, a pré-visualização da política é mostrada no formato de texto e não é editável.
    
1.  **Selecione Refresh** para ver a política configurada.

## <a name="next-steps"></a>Passos seguintes

- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Atestar um enclave SGX usando amostras de código](/samples/browse/?expanded=azure&terms=attestation)

