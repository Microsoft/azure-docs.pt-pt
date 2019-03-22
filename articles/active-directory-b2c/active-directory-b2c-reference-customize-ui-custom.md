---
title: Personalizar a interface do Usuário de um percurso do utilizador com as políticas personalizadas | Documentos da Microsoft
description: Saiba mais sobre as políticas personalizadas do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a51c2987781184539edfcc8be9b9f483a215a157
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122931"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Personalizar a interface do Usuário de um percurso do utilizador com as políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Este artigo é uma descrição avançada de como funciona a personalização da interface do Usuário e como ativar com políticas personalizadas do Azure AD B2C, com o Framework de experiência de identidade.


Uma experiência de utilizador totalmente integrada é fundamental para qualquer solução de empresa-consumidor. Uma experiência de utilizador totalmente integrada é uma experiência, seja no dispositivo ou um browser, onde a jornada de um utilizador através do serviço é indissociável do que o serviço de cliente que estão a utilizar.

## <a name="understand-the-cors-way-for-ui-customization"></a>Compreender a forma CORS para personalização da interface do Usuário

O Azure AD B2C permite-lhe personalizar o aspeto e funcionalidade da experiência de usuário (UX) nas várias páginas que são servidos e apresentados pelo Azure AD B2C com suas políticas personalizadas.

Para essa finalidade, o Azure AD B2C executa o código no browser do consumidor e utiliza a abordagem moderna e standard [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/) para carregar conteúdo personalizado a partir de uma URL específica que especificar numa política personalizada para apontar para os modelos de HTML5/CSS. CORS é um mecanismo que permite recursos restritos, como fontes, numa página da web para ser pedidos a partir de outro domínio fora do domínio a partir do qual o recurso teve origem.

Em comparação com a maneira tradicional antiga, onde as páginas de modelo são propriedade pela solução em que que forneceu limitada de texto e imagens, em que o controle limitado de layout e a funcionalidade foi oferecido que leva a mais que os problemas para obter uma experiência totalmente integrada, a forma CORS oferece suporte a HTML5 e CSS e permitem-lhe:

- Alojar o conteúdo e a solução injeta seus controles usando o script do lado do cliente.
- Tem controlo total sobre cada pixel de layout e a funcionalidade.

Pode fornecer a quantas páginas de conteúdo quanto desejar criar arquivos de HTML5/CSS, conforme apropriado.

> [!NOTE]
> Por motivos de segurança, o uso de JavaScript está bloqueado para personalização. 

Em cada um dos seus modelos de HTML5/CSS, fornece um *âncora* elemento, que corresponde ao necessários `<div id=”api”>` elemento no HTML ou a página de conteúdo como ilustrar daqui em diante. O Azure AD B2C requer que todas as páginas de conteúdo têm esta div que específico.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

Conteúdo do Azure AD B2C relacionadas para a página é injetado nessa div, enquanto o resto da página é sua para controlar o limite de tempo. O código JavaScript do Azure AD B2C extrai o conteúdo e injeta o HTML para este elemento div específico. O Azure AD B2C injeta os seguintes controlos conforme apropriado: controlo de Seletor de conta, inicie sessão em controles, multi-factor (atualmente phone controles baseados em) e controles de coleção de atributo. O Azure AD B2C garante que todos os controles são HTML5 acessível e não compatíveis, todos os controles podem ser totalmente com o estilo e que uma versão de controlo não diminua.

O conteúdo intercalado, eventualmente, é apresentado como o documento dinâmico para o consumidor.

Para garantir que tudo funciona conforme esperado, tem de:

- Certifique-se de que seu conteúdo é HTML5 acessível e não compatíveis
- Certifique-se de que o seu servidor de conteúdos está ativado para CORS.
- Servir conteúdo através de HTTPS.
- Utilizar URLS absolutos como `https://yourdomain/content` para todas as ligações e conteúdo CSS.

> [!TIP]
> Para verificar se o site está a alojar o seu conteúdo em tem CORS ativada e solicitações CORS de teste, pode utilizar o site https://test-cors.org/. Graças a este site, pode enviar o pedido CORS para um servidor remoto (para testar se o CORS é suportado) ou enviar o pedido CORS para um servidor de teste (para explorar algumas funcionalidades do CORS).

> [!TIP]
> O site https://enable-cors.org/ também constitui um mais do que recursos úteis no CORS.

Graças a essa abordagem baseada em CORS, os usuários finais ter experiências consistentes entre a aplicação e o número de páginas apresentados pelo Azure AD B2C.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Como pré-requisito, tem de criar uma conta de armazenamento. Precisa de uma subscrição do Azure para criar uma conta de armazenamento de Blobs do Azure. Pode inscrever-se numa avaliação gratuita no [Web site do Azure](https://azure.microsoft.com/pricing/free-trial/).

1. Abra uma sessão de navegação e navegue para o [portal do Azure](https://portal.azure.com).
2. Inicie sessão com as suas credenciais administrativas.
3. Clique em **criar um recurso** > **armazenamento** > **conta de armazenamento**.  R **criar conta de armazenamento** painel abre-se.
4. Na **Name**, forneça um nome para a conta de armazenamento, por exemplo, *contoso369b2c*. Este valor mais tarde é referido como a *storageAccountName*.
5. Escolha as seleções adequadas para o escalão de preço, o grupo de recursos e a subscrição. Certifique-se de que tem o **afixar ao Startboard** opção marcada. Clique em **Criar**.
6. Volte ao Startboard e clique na conta de armazenamento que criou.
7. Na **serviços** secção, clique em **Blobs**. R **painel de serviço Blob** abre-se.
8. Clique em **+ contentor**.
9. Na **Name**, forneça um nome para o contentor, por exemplo, *b2c*. Este valor mais tarde é referido como *containerName*.
9. Selecione **Blob** como o **acessar tipo**. Clique em **Criar**.
10. O contentor que criou aparece na lista no **painel de serviço Blob**.
11. Fechar o **Blobs** painel.
12. Sobre o **painel de conta de armazenamento**, clique no **chave** ícone. Uma **painel de chaves de acesso** abre-se.  
13. Anote o valor de **chave1**. Este valor é chamado posteriormente *chave1*.

## <a name="downloading-the-helper-tool"></a>Baixar a ferramenta de programa auxiliar

1.  Transfira a ferramenta auxiliar partir [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Guardar a *B2C-AzureBlobStorage-Client-Master* ficheiro no seu computador local.
3.  Extrair o conteúdo do ficheiro B2C-AzureBlobStorage-Client-Master no seu disco local, por exemplo sob a **pacote de personalização da interface do Usuário** pasta, que cria um *B2C-AzureBlobStorage-Client-master*pasta por baixo.
4.  Abra nessa pasta e extrair o conteúdo do ficheiro de arquivo *B2CAzureStorageClient.zip* dentro da mesma.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Carregar os ficheiros de exemplo de pacote de personalizado da interface do Usuário

1.  Através do Explorador do Windows, navegue para a pasta *B2C-AzureBlobStorage-Client-mestre* localizada sob o *pacote de personalização da interface do Usuário* pasta que criou na secção anterior.
2.  Executar o *B2CAzureStorageClient.exe* ficheiro. Este programa carrega todos os ficheiros no diretório que especificar para a sua conta de armazenamento e ativar o acesso CORS para esses ficheiros.
3.  Quando lhe for pedido, especifique: um.  O nome da sua conta de armazenamento *storageAccountName*, por exemplo *contoso369b2c*.
    b.  A chave de acesso primária do seu armazenamento de Blobs do azure, *chave1*, por exemplo *contoso369b2c*.
    c.  O nome do seu contentor de armazenamento de BLOBs de armazenamento *containerName*, por exemplo *b2c*.
    d.  O caminho da *pacote de iniciante* exemplo ficheiros, por exemplo *.... \B2CTemplates\wingtiptoys*.

Se tiver seguido os passos anteriores, os ficheiros de HTML5 e CSS do *pacote de personalização da interface do Usuário* para a empresa fictícia **wingtiptoys** agora está a apontar para a sua conta de armazenamento.  Pode verificar que o conteúdo tiver sido carregado corretamente ao abrir o painel de contentor relacionados no portal do Azure. Em alternativa, pode verificar que o conteúdo tiver sido carregado corretamente ao aceder à página a partir de um browser. Para obter mais informações, consulte [Azure Active Directory B2C: Uma ferramenta de programa auxiliar usada para demonstrar a funcionalidade de personalização de interface (IU) de utilizador de página](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Certifique-se de que a conta de armazenamento tem a CORS ativada

CORS (partilha de recursos de várias origens) tem de estar ativada no seu ponto final para o Azure AD B2C carregar o seu conteúdo. Isto acontece porque o conteúdo está hospedado num domínio diferente do domínio que do Azure AD B2C irá servir a página da.

Para verificar que o armazenamento que está a alojar o seu conteúdo em tem a CORS ativada, continue com os passos seguintes:

1. Abra uma sessão de navegação e navegue para a página *unified.html* utilizando o URL completo do seu local na sua conta de armazenamento, `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Por exemplo, https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Navegue para https://test-cors.org. Este site permite-lhe verificar se a página que está a utilizar tem a CORS ativada.  
   <!--
   ![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
   -->

3. Na **URL remoto**, introduza o URL completo para o seu conteúdo unified.html e clique em **enviar pedido**.
4. Certifique-se de que a saída na **resultados** secção contém *Estado XHR: 200*, que indica que o CORS está ativado.
   <!--
   ![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
   -->
   A conta de armazenamento deve conter, agora, um contentor de BLOBs denominado *b2c* na ilustração que contém os seguintes modelos wingtiptoys a *pacote de iniciante*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

A tabela seguinte descreve a finalidade das páginas HTML5 anteriores.

| Modelo de HTML5 | Descrição |
|----------------|-------------|
| *phonefactor.html* | Esta página pode ser utilizada como um modelo para uma página de autenticação multifator. |
| *resetpassword.html* | Esta página pode ser utilizada como modelo para um Esqueceu-se a página de palavra-passe. |
| *selfasserted.html* | Esta página pode ser utilizada como um modelo para uma conta de redes sociais Inscreva-se a página, uma página inscrição conta local ou uma página de início de sessão da conta local. |
| *unified.html* | Esta página pode ser utilizada como um modelo para um início de sessão unificada ou a página de início de sessão. |
| *updateprofile.html* | Esta página pode ser utilizada como um modelo para uma página de atualização de perfil. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Adicionar uma ligação para os modelos de HTML5/CSS para o seu percurso do utilizador

Pode adicionar uma ligação aos seus modelos de HTML5/CSS para o seu percurso do utilizador editando diretamente uma política personalizada.

Os modelos de HTML5/CSS personalizados para utilizar no seu percurso do utilizador tem de ser especificado numa lista de definições de conteúdo que podem ser utilizadas nesses jornadas de utilizador. Para essa finalidade, opcional *<ContentDefinitions>* elemento XML tem de ser declarado sob o *<BuildingBlocks>* secção do seu ficheiro XML de política personalizada.

A tabela seguinte descreve o conjunto de conteúdo, mecanismo e o tipo de páginas que está relacionado a eles experiência de IDs de definição reconhecidos pela identidade do Azure AD B2C.

| ID de definição de conteúdo | Descrição |
|-----------------------|-------------|
| *api.error* | **Página de erro**. Esta página é apresentada quando é encontrado uma exceção ou um erro. |
| *api.idpselections* | **Página de seleção do fornecedor de identidade**. Esta página contém uma lista de fornecedores de identidade que o utilizador pode escolher durante o início de sessão. Esses provedores são a fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook e Google + ou contas locais (com base no nome de utilizador ou endereço de e-mail). |
| *api.idpselections.signup* | **Seleção de fornecedor de identidade para inscrição**. Esta página contém uma lista de fornecedores de identidade que o utilizador pode escolher entre durante a inscrição. Esses provedores são a fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook e Google + ou contas locais (com base no nome de utilizador ou endereço de e-mail). |
| *api.localaccountpasswordreset* | **Esqueceu-se a página de palavra-passe**. Esta página contém um formulário que o utilizador tem de preencher para iniciar sua reposição de palavra-passe.  |
| *api.localaccountsignin* | **Página de início de sessão de conta local**. Esta página contém um formulário de início de sessão que o utilizador tem de preencher quando iniciar sessão com uma conta local que se baseia num endereço de e-mail ou um nome de utilizador. O formulário pode conter uma caixa de entrada de texto e a caixa de entrada de palavra-passe. |
| *api.localaccountsignup* | **Página de inscrição de conta local**. Esta página contém um formulário de inscrição que o utilizador tem para preencher ao inscrever-se de uma conta local que se baseia num endereço de e-mail ou um nome de utilizador. O formulário pode conter controles de entrada diferentes, como a caixa de entrada de texto, caixa de entrada de palavra-passe, o botão de rádio, caixas de lista pendente de seleção única e caixas de verificação de seleção múltipla. |
| *api.phonefactor* | **Página do multi-factor authentication**. Nesta página, os usuários podem verificar seus números de telefone (usando o Editor de texto ou de voz) durante a inscrição ou início de sessão. |
| *api.selfasserted* | **Página de inscrição de conta de redes sociais**. Esta página contém um formulário de inscrição que o utilizador tem para preencher ao inscrever-se com uma conta existente de um fornecedor de identidade de redes sociais como o Facebook ou o Google +. Esta página é semelhante para a página de inscrição de conta de redes sociais anterior com a exceção os campos de entrada de palavra-passe. |
| *api.selfasserted.profileupdate* | **Página de atualização de perfil**. Esta página contém um formulário que o utilizador pode utilizar para atualizar seu perfil. Esta página é semelhante para a página de inscrição de conta de redes sociais anterior com a exceção os campos de entrada de palavra-passe. |
| *api.signuporsignin* | **Página de inscrição ou início de sessão unificada**.  Esta página processa ambos Inscreva-se e inícios de sessão de utilizadores, que podem utilizar fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook ou Google + ou contas locais.

## <a name="next-steps"></a>Passos Seguintes
[Referência: Compreender as políticas personalizadas como funcionam com o Framework de experiência de identidade no B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
