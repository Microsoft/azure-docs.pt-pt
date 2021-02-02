---
title: Configurar a Azure Attestation com o portal Azure
description: Como configurar e configurar um provedor de atestado utilizando o portal Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 86adac557c6de133e95e97bfedbd302cc6a2b27e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429162"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Quickstart: Configurar a Azure Attestation com o portal Azure

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Siga os passos abaixo para gerir um provedor de atestado usando o portal Azure.

## <a name="1-attestation-provider"></a>1. Provedor de Attestation

### <a name="11-create-an-attestation-provider"></a>1.1 Criar um fornecedor de atestado

#### <a name="111-to-configure-the-provider-with-unsigned-policies"></a>1.1.1 Configurar o prestador com políticas não assinadas

1.  A partir do menu do portal Azure, ou a partir da página Inicial, **selecione Criar um recurso**
2.  Na caixa de pesquisa, insira **atestado**
3.  Na lista de resultados, escolha **Microsoft Azure Attestation**
4.  Na página da Microsoft Azure Attestation, escolha **Criar**
5.  Na página do provedor de atestado Create, forneça as seguintes entradas:
    
    **Assinatura**: Escolha uma subscrição
    
    **Grupo de Recursos**: selecione um grupo de recursos existente ou escolha **Criar novo** e insira um nome de grupo de recursos
    
    **Nome**: É necessário um nome único

    **Localização**: escolha um local 
    
    **Ficheiro de certificados de signatário de** política : Não carregumente o ficheiro de certificados de sinalização de política para configurar o fornecedor com políticas não assinadas 
6.  Depois de fornecer as entradas necessárias, clique em **Review+Create**
7.  Corrija problemas de validação se houver e clique em **Criar**.

#### <a name="112-to-configure-the-provider-with-signed-policies"></a>1.1.2 Configurar o prestador com políticas assinadas

1.  A partir do menu do portal Azure, ou a partir da página Inicial, **selecione Criar um recurso**
2.  Na caixa de pesquisa, insira **atestado**
3.  Na lista de resultados, escolha **Microsoft Azure Attestation**
4.  Na página da Microsoft Azure Attestation, escolha **Criar**
5.  Na página do provedor de atestado Create, forneça as seguintes informações:
    
    a. **Assinatura**: Escolha uma subscrição
    
    b. **Grupo de Recursos**: selecione um grupo de recursos existente ou escolha **Criar novo** e insira um nome de grupo de recursos
    
    c. **Nome**: É necessário um nome único

    d. **Localização**: escolha um local 
    
    e. **Ficheiro de certificados de sinalização de** política : Para configurar o fornecedor de atestado com certificados de assinatura de apólice, carregundo o ficheiro certs. Ver exemplos [aqui](./policy-signer-examples.md) 
6.  Depois de fornecer as entradas necessárias, clique em **Review+Create**
7.  Corrija problemas de validação se houver e clique em **Criar**.

### <a name="12-view-attestation-provider"></a>1.2 Visualizar provedor de atestado

1.  A partir do menu do portal Azure, ou da página Inicial, selecione **Todos os recursos**
2.  Na caixa de filtro, insira o nome do provedor de atestado e selecione-o

### <a name="13-delete-attestation-provider"></a>1.3 Eliminar o fornecedor de atestado

1.  A partir do menu do portal Azure, ou da página Inicial, selecione **Todos os recursos**
2.  Na caixa de filtro, insira o nome do provedor de atestado
3.  Selecione a caixa de verificação e clique em **Eliminar**
4.  Digite sim e clique em **Eliminar** [OR]
1.  A partir do menu do portal Azure, ou da página Inicial, selecione **Todos os recursos**
2.  Na caixa de filtro, insira o nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue para a página geral
4.  Clique em **Eliminar** no menu superior e clique em **Sim**


## <a name="2-attestation-policy-signers"></a>2. Signatários da política de atestado

### <a name="21-view-policy-signer-certificates"></a>2.1 Ver certificados de sinalizadores de política

1.  A partir do menu do portal Azure, ou da página Inicial, selecione **Todos os recursos**
2.  Na caixa de filtro, insira o nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue para a página geral
4.  Clique em **certificados de sinalizador** de política no menu de recursos do lado esquerdo ou no painel inferior
5.  Clique **em Baixar certificados de sinalizador de política** de descarregamento (O botão será desativado para os fornecedores de atestado criados sem o requisito de assinatura de políticas)
6.  O ficheiro de texto descarregado terá todos os certificados num formato JWS.
a.  Verifique a contagem de certificados e os certificados descarregados.

### <a name="22-add-policy-signer-certificate"></a>2.2 Adicionar certificado de sinalizador de política

1.  A partir do menu do portal Azure, ou da página Inicial, selecione **Todos os recursos**
2.  Na caixa de filtro, insira o nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue para a página geral
4.  Clique em **certificados de sinalizador** de política no menu de recursos do lado esquerdo ou no painel inferior
5.  Clique em **Adicionar** no menu superior (O botão será desativado para os fornecedores de atestado criados sem o requisito de assinatura de políticas)
6.  Faça upload do ficheiro de certificado do signatário da política e clique em **Adicionar**. Ver exemplos [aqui](./policy-signer-examples.md)

### <a name="23-delete-policy-signer-certificate"></a>2.3 Apagar certificado de signatário de política

1.  A partir do menu do portal Azure, ou da página Inicial, selecione **Todos os recursos**
2.  Na caixa de filtro, insira o nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue para a página geral
4.  Clique em **certificados de sinalizador** de política no menu de recursos do lado esquerdo ou no painel inferior
5.  Clique em **Eliminar** no menu superior (O botão será desativado para os fornecedores de atestado criados sem o requisito de assinatura de políticas)
6.  Faça upload do ficheiro de certificado do signatário da política e clique em **Eliminar**. Ver exemplos [aqui](./policy-signer-examples.md) 

## <a name="3-attestation-policy"></a>3. Política de Atestado

### <a name="31-view-attestation-policy"></a>3.1 Ver política de atestado

1.  A partir do menu do portal Azure, ou da página Inicial, selecione **Todos os recursos**
2.  Na caixa de filtro, insira o nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue para a página geral
4.  Clique em **Política** no menu de recursos do lado esquerdo ou no painel inferior
5.  Selecione o **Tipo de Attestation** preferido e veja a **política atual**

### <a name="32-configure-attestation-policy"></a>3.2 Política de atestação de configure

#### <a name="321-when-attestation-provider-is-created-without-policy-signing-requirement"></a>3.2.1 Quando o prestador de atestado é criado sem requisito de assinatura de políticas

##### <a name="upload-policy-in-jwt-format"></a>Política de upload em formato JWT

1.  A partir do menu do portal Azure, ou da página Inicial, selecione **Todos os recursos**
2.  Na caixa de filtro, insira o nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue para a página geral
4.  Clique em **Política** no menu de recursos do lado esquerdo ou no painel inferior
5.  Clique em **Configurar** no menu superior
6.  Quando o fornecedor de atestado é criado sem o requisito de assinatura de políticas, o utilizador pode carregar uma política em formato **JWT** ou **Text**
7.  Selecione **o Formato de Política** como **JWT**
8.  Faça upload do ficheiro de política com conteúdo de política num formato **JWT não assinado/assinado** e clique em **Guardar**. Ver exemplos [aqui](./policy-examples.md)
    
    Para a opção de upload de ficheiros, a pré-visualização da política será mostrada no formato de texto e a pré-visualização da política não é editável.

7.  Clique em **Atualizar** no menu superior para ver a política configurada

##### <a name="upload-policy-in-text-format"></a>Política de upload em formato de texto

1.  A partir do menu do portal Azure, ou da página Inicial, selecione **Todos os recursos**
2.  Na caixa de filtro, insira o nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue para a página geral
4.  Clique em **Política** no menu de recursos do lado esquerdo ou no painel inferior
5.  Clique em **Configurar** no menu superior
6.  Quando o fornecedor de atestado é criado sem o requisito de assinatura de políticas, o utilizador pode carregar uma política em formato **JWT** ou **Text**
7.  Selecione **o formato de política** como **texto**
8.  Faça upload do ficheiro de política com conteúdo em formato **de texto** ou introduza o conteúdo da política na área de texto e clique em **Guardar**. Ver exemplos [aqui](./policy-examples.md)

    Para a opção de upload de ficheiros, a pré-visualização da política será mostrada no formato de texto e a pré-visualização da política não é editável.

8.  Clique **em Refresh** para ver a política configurada

#### <a name="322-when-attestation-provider-is-created-with-policy-signing-requirement"></a>3.2.2 Quando o prestador de atestado é criado com requisito de assinatura de políticas

##### <a name="upload-policy-in-jwt-format"></a>Política de upload em formato JWT

1.  A partir do menu do portal Azure, ou da página Inicial, selecione **Todos os recursos**
2.  Na caixa de filtro, insira o nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue para a página geral
4.  Clique em **Política** no menu de recursos do lado esquerdo ou no painel inferior
5.  Clique em **Configurar** no menu superior
6.  Quando o fornecedor de atestado é criado com o requisito de assinatura de políticas, o utilizador só pode carregar uma política no **formato JWT assinado**
7.  O ficheiro de política de upload está **assinado no formato JWT** e clique em **Guardar**. Ver exemplos [aqui](./policy-examples.md)

    Para a opção de upload de ficheiros, a pré-visualização da política será mostrada no formato de texto e a pré-visualização da política não é editável.
    
8.  Clique **em Refresh** para ver a política configurada

## <a name="next-steps"></a>Passos seguintes

- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Atestar um enclave SGX usando amostras de código](/samples/browse/?expanded=azure&terms=attestation)

