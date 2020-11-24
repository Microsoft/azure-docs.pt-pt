---
title: Sign-on único baseado em cabeçalho para aplicativos no local com Proxy app AD app Azure
description: Saiba como fornecer um único sinal de inscrição para aplicações no local que são protegidas com autenticação baseada em cabeçalho.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: d39d72a79d4b273918986d0d350df4706592c77d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503174"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy-preview"></a>Sign-on único baseado em cabeçalho para aplicativos no local com Proxy app AD Ad (Preview)

Azure Ative Directory (Azure AD) Application Proxy suporta de forma nativa o acesso de assinatura única a aplicações que usam cabeçalhos para autenticação. Pode configurar os valores do cabeçalho exigidos pela sua aplicação no Azure AD. Os valores do cabeçalho serão enviados para a aplicação através do Application Proxy. Alguns benefícios para a utilização de suporte nativo para autenticação baseada em cabeçalho com Application Proxy incluem:  

* **Simplificar o acesso remoto às suas aplicações no local** - App Proxy permite-lhe simplificar a arquitetura de acesso remoto existente. Pode substituir o acesso VPN a estas aplicações. Também pode remover as dependências de soluções de identidade no local para a autenticação. Os seus utilizadores não notarão nada de diferente quando iniciarem saturação para utilizarem as suas aplicações corporativas. Ainda podem trabalhar em qualquer lugar em qualquer dispositivo.  

* **Nenhum software adicional ou alterações nas suas apps** - Pode utilizar os conectores Proxy da aplicação existentes e não necessita de nenhum software adicional para ser instalado.  

* **Ampla lista de atributos e transformações disponíveis** - Todos os valores de cabeçalho disponíveis são baseados em alegações padrão emitidas pela Azure AD. Todos os atributos e transformações disponíveis para [configurar reclamações para aplicações SAML ou OIDC](../develop/active-directory-saml-claims-customization.md#attributes) também estão disponíveis para serem usados como valores de cabeçalho. 

## <a name="pre-requisites"></a>Pré-requisitos
Antes de iniciar com um único sinal para aplicações de autenticação baseadas em cabeçalhos, certifique-se de que o seu ambiente está pronto com as seguintes definições e configurações:
- Tem de ativar o Application Proxy e instalar um conector que tenha linha de site para as suas aplicações. Consulte o tutorial [Adicione uma aplicação no local para acesso remoto através do Application Proxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) para aprender a preparar o ambiente no local, instalar e registar um conector e testar o conector. 

## <a name="supported-capabilities"></a>Capacidades suportadas

A tabela que se segue lista as capacidades comuns necessárias para aplicações de autenticação baseadas em cabeçalhos que são suportadas com o Application Proxy. 

|Requisito   |Description|
|----------|-----------|
|SSO federado |No modo pré-autenticado, todas as aplicações estão protegidas com a autenticação AZure AD e permitem que os utilizadores tenham uma única sação. |
|Acesso remoto |O Proxy da aplicação permite o acesso remoto à aplicação. Os utilizadores podem aceder à aplicação a partir da internet em qualquer navegador usando o URL Externo. A Aplicação Proxy não se destina a uso de acesso corporativo. |
|Integração baseada em cabeçalhos |Application Proxy faz a integração SSO com Azure AD e, em seguida, passa identidade ou outros dados de aplicação como cabeçalhos HTTP para a aplicação. |
|Autorização de pedido |As políticas comuns podem ser especificadas com base na aplicação que está a ser acedida, na adesão do grupo do utilizador e noutras políticas. No Azure AD, as políticas são implementadas utilizando [acesso condicional](../conditional-access/overview.md). As políticas de autorização de pedido aplicam-se apenas ao pedido inicial de autenticação. |
|Autenticação intensificada |As políticas podem ser definidas para forçar a autenticação adicionada, por exemplo, para ter acesso a recursos sensíveis. |
|Autorização de grãos finos |Fornece controlo de acesso ao nível de URL. As políticas adicionais podem ser aplicadas com base no url que está a ser acedido. O URL interno configurado para a app, define o âmbito da aplicação a que a política é aplicada. A política configurada para o caminho mais granular é aplicada.  |

> [!NOTE] 
> Este artigo apresenta aplicações de autenticação baseadas em cabeçalho ao Azure AD usando o Application Proxy e é o padrão recomendado. Como alternativa, existe também um padrão de integração que utiliza o PingAccess com AZure AD para permitir a autenticação baseada em cabeçalho. Para obter mais detalhes, consulte [a autenticação baseada no cabeçalho para um único sinal com Proxy de aplicação e PingAccess](application-proxy-ping-access-publishing-guide.md).

## <a name="how-it-works"></a>Como funciona

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="Como funciona o único sinal baseado no cabeçalho com o Application Proxy." lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. O Administrador personaliza os mapeamentos de atributos exigidos pela aplicação no portal AD AZure. 
2. Quando um utilizador acede à aplicação, o Application Proxy garante que o utilizador é autenticado pela Azure AD 
3. O serviço de nuvem Proxy application está ciente dos atributos necessários. Assim, o serviço recolhe as reclamações correspondentes do token de ID recebido durante a autenticação. Em seguida, o serviço traduz os valores nos cabeçalhos HTTP necessários como parte do pedido ao Conector. 
4. O pedido é então transmitido ao Conector, que é depois passado para o pedido de backend. 
5. A aplicação recebe os cabeçalhos e pode usar estes cabeçalhos conforme necessário. 

## <a name="publish-the-application-with-application-proxy"></a>Publicar a aplicação com Application Proxy

1. Publique a sua aplicação de acordo com as instruções descritas em [Publicar aplicações com Aplicação Proxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).  
    - O valor URL interno determina o âmbito da aplicação. Se configurar o valor do URL interno no caminho raiz da aplicação, então todos os sub-caminhos por baixo da raiz receberão a mesma configuração do cabeçalho e outra configuração de aplicação. 
    - Crie uma nova aplicação para definir uma configuração de cabeçalho diferente ou atribuição do utilizador para um caminho mais granular do que a aplicação configurada. Na nova aplicação, configugue o URL interno com o caminho específico que necessita e, em seguida, configugue os cabeçalhos específicos necessários para este URL. O Proxy de aplicação irá sempre corresponder às definições de configuração com o caminho mais granular definido para uma aplicação. 

2. Selecione **O Diretório Ativo Azure**   como método de **pré-autenticação**. 
3. Atribua um utilizador de teste navegando aos **Utilizadores e grupos** e atribuindo os utilizadores e grupos apropriados. 
4. Abra um browser e navegue para o **URL Externo** a partir   das definições de Procuração de Aplicações. 
5. Verifique se pode ligar-se à aplicação. Mesmo que consiga ligar, ainda não pode aceder à aplicação, uma vez que os cabeçalhos não estão configurados. 

## <a name="configure-single-sign-on"></a>Configurar o início de sessão único 
Antes de iniciar com um único sinal para aplicações baseadas em cabeçalhos, já deveria ter instalado um conector Proxy de aplicação e o conector pode aceder às aplicações-alvo. Caso contrário, siga os passos em [Tutorial: Azure AD Application Proxy](application-proxy-add-on-premises-application.md)   e volte aqui. 

1. Depois de a sua aplicação aparecer na lista de aplicações da empresa, selecione-a e selecione **'S-on' único**. 
2. Descreva o único modo de inscrição **para cabeçalho .** 
3. Na Configuração Básica, **o Azure Ative Directory,** será selecionado como padrão. 
4. Selecione o lápis de edição, em Cabeçalhos para configurar cabeçalhos para enviar para a aplicação. 
5. **Selecione Adicione novo cabeçalho**. Forneça um **Nome** para o cabeçalho e selecione **o Atributo** ou **a Transformação** e selecione a partir do drop-down que o cabeçalho de que a sua aplicação necessita.  
    - Para saber mais sobre a lista de atributos disponíveis, consulte [As Personalizações de Reclamações- Atributos](../develop/active-directory-saml-claims-customization.md#attributes). 
    - Para saber mais sobre a lista de transformação disponível, consulte [As Personalizações de Reclamações- Claim Transformations](../develop/active-directory-saml-claims-customization.md#claim-transformations). 
    - Pode ainda adicionar um **Cabeçalho de Grupo**, para enviar todos os grupos de que um utilizador faz parte, ou os grupos designados para a aplicação como cabeçalho. Para saber mais sobre a configuração de grupos como valor ver: [Configurar pedidos de grupo para aplicações](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration). 
6. Selecione Guardar. 

## <a name="test-your-app"></a>Testar a aplicação 

Quando tiver completado todos estes passos, a sua aplicação deve estar a correr e disponível. Para testar a aplicação: 
1. Abra um novo navegador ou janela de navegador privado para se certificar de que os cabeçalhos previamente em cache estão limpos. Em seguida, navegue para o **URL Externo** a partir   das definições de Procuração de Aplicação.
2. Inscreva-se na conta de teste que atribuiu à aplicação. Se conseguir carregar e assinar a aplicação usando SSO, então está bem! 


## <a name="next-steps"></a>Passos seguintes

- [O que é o início de sessão único?](what-is-single-sign-on.md)
- [O que é procuração de aplicativos?](what-is-application-proxy.md)
- [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)
