---
title: Adicionar identidade visual à página da sua organização início de sessão - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como adicionar a imagem corporativa da sua organização para a página de início de sessão no Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: lizross
ms.reviewer: kexia
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fb657d2c2cd4eeae0eb96dd37505a1c47278725
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103065"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Adicionar identidade visual à página da sua organização do Azure Active Directory início de sessão
Utilize o logótipo e esquemas de cores personalizada da sua organização para fornecer uma consistente aspeto e funcionalidade em suas páginas de início de sessão no Azure Active Directory (Azure AD). As páginas de início de sessão são apresentadas quando os utilizadores iniciam sessão em aplicações de baseada na web da sua organização, como o Office 365, que utiliza o Azure AD como fornecedor de identidade.

>[!Note]
>Adicionar a imagem corporativa personalizada tem de utilizar o Azure Active Directory Premium 1, 2 de Premium ou básicas editions, ou ter uma licença do Office 365. Para obter mais informações sobre as edições e licenciamento, consulte [Inscreva-se para o Azure AD Premium](active-directory-get-started-premium.md).<br><br>As edições Premium e Básica do Azure AD estão disponíveis para clientes na China que utilizem a instância mundial do Azure Active Directory. As edições do Azure AD Premium e Básico não são atualmente suportadas pelo serviço do Azure operado pela 21Vianet na China. Para obter mais informações, contacte-nos através do [Fórum do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Personalizar a página de início de sessão do Azure AD
Pode personalizar sua do Azure AD início de sessão páginas, que são apresentados quando os utilizadores iniciam sessão aplicações de inquilino específico da sua organização, tal como [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com), ou quando passar uma variável de domínio, tal como [ *https://passwordreset.microsoftonline.com/?whr=contoso.com*](https://passwordreset.microsoftonline.com/?whr=contoso.com).

A imagem corporativa personalizada deixa de aparecer imediatamente quando os utilizadores aceda a sites como www\.office.com. Em vez disso, o utilizador tem início de sessão antes da imagem corporativa personalizada é apresentada.

> [!NOTE]
> Todos os elementos de identidade visual são opcionais. Por exemplo, se especificar um logótipo de faixa com nenhuma imagem de fundo, a página de início de sessão irá mostrar o logótipo com uma imagem de fundo predefinido do site de destino (por exemplo, o Office 365).<br><br>Além disso, imagem corporativa a página de início de sessão não passa para contas Microsoft pessoais. Se os usuários ou convidados empresariais iniciar sessão com uma conta Microsoft pessoal, a página de início de sessão não reflete a imagem corporativa da sua organização.

### <a name="to-customize-your-branding"></a>Para personalizar a imagem corporativa
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**e, em seguida, selecione **imagem corporativa da empresa**e, em seguida, selecione **configurar**.

    ![Contoso - página de imagem corporativa da empresa, a opção de configurar realçada](media/customize-branding/company-branding-configure-button.png)

3. Sobre o **configurar imagem corporativa da empresa** , indique uma ou todas as informações seguintes.

    >[!Important]
    >Todas as imagens personalizadas, adicionar esta página tem o tamanho da imagem (pixels) e ficheiros potencialmente tamanho (KB), restrições. Devido a essas restrições, a maioria provavelmente terá de utilizar um editor de fotografia para criar as imagens de tamanho certo.

    - **Definições gerais**

        ![Configurar imagem corporativa página, com as definições gerais concluídas](media/customize-branding/configure-company-branding-general-settings.png)

        - **Idioma.** A linguagem é automaticamente definida como padrão e não pode ser alterada.
        
        - **Imagem de fundo de página de início de sessão.** Selecione um ficheiro de imagem PNG ou. jpg a aparecer como plano de fundo para as páginas de início de sessão. 
        
            A imagem não pode ser maior do que 1920 x 1080 pixels no tamanho e tem de ter um tamanho de ficheiro de menos de 300 KB.

        - **Logótipo de faixa.** Selecione uma versão. png ou. jpg do seu logótipo apareça na página de início de sessão após o utilizador introduzir um nome de utilizador e no **as minhas aplicações** página do portal.
            
            A imagem não pode ser aumentasse que 36 pixels ou maior do que 245 pixels. Recomendamos que utilize uma imagem transparente, uma vez que o plano de fundo pode não corresponder aos seu plano de fundo do logótipo. Também recomendamos que não adicione preenchimento em torno da imagem ou pode fazer o logótipo parecer pequeno.

        - **Sugestão de nome de utilizador.** Escreva o texto de sugestão que aparece aos utilizadores se esquecerem o respetivo nome de utilizador. Este texto deve ser o Unicode, sem ligações ou código e não pode exceder os 64 carateres. Se os convidados iniciarem sessão sua aplicação, sugerimos que não a adicionar desta sugestão.

        - **Texto da página de início de sessão.** Escreva o texto que aparece na parte inferior da página de início de sessão. Pode utilizar este texto para comunicar informações adicionais, como o número de telefone para o suporte técnico ou uma instrução legal. Este texto deve ser Unicode e não pode exceder 256 carateres. Sugerimos também não incluindo ligações ou tags de HTML.

    - **Definições avançadas**
            
        ![Configurar imagem corporativa página, com definições avançadas foi concluídas](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Cor de fundo de página de início de sessão.** Especificar a cor hexadecimal (por exemplo, branco é #FFFFFF) que irá aparecer em vez de sua imagem de fundo em situações de ligação de largura de banda baixa. Recomendamos que utilize a cor primária do logótipo de faixa ou a cor da organização.

        - **Imagem do logótipo quadrado.** Selecione um formato. PNG (preferidas) ou. jpg imagem do logótipo da sua organização para que sejam apresentadas aos utilizadores durante o processo de instalação para novos dispositivos Windows 10 Enterprise. Esta imagem só é utilizada para a autenticação do Windows e é apresentada apenas nos inquilinos que utilizam [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) experiências de páginas em outros com o Windows 10 para a implementação ou para a entrada de palavra-passe.
        
            A imagem não pode ser maior do que os pixels de 240 x 240 no tamanho e tem de ter um tamanho de ficheiro de menos de 10 KB. Recomendamos que utilize uma imagem transparente, uma vez que o plano de fundo pode não corresponder aos seu plano de fundo do logótipo. Também recomendamos que não adicione preenchimento em torno da imagem ou pode fazer o logótipo parecer pequeno.
    
        - **Imagem do logótipo quadrado, tema escuro.** Mesmo que a imagem do logótipo quadrado acima. Esta imagem de logótipo substitui a imagem do logótipo quadrado quando utilizado com um plano de fundo escuro, tal como com Windows 10 do Azure AD associado a um telas durante a experiência de out-of-box (OOBE).  Se o seu logótipo parece bem em fundos branco, escuros azuis e pretas, não precisa de adicionar esta imagem. 
        
        - **Mostre a opção para manter a sessão iniciada.** Pode optar por permitir que os utilizadores a manterem a sessão iniciada para o Azure AD até explicitamente a terminar a sessão. Se escolher **não**, esta opção está oculto e os utilizadores devem iniciar sessão sempre que o browser é fechado e reaberto.
        
            >[!Note]
            >Algumas funcionalidades do SharePoint Online e do Office 2010 dependem da capacidade de os utilizadores poderem escolher manter a sessão iniciada. Se tiver definido esta opção como **Não**, os utilizadores poderão ver avisos adicionais e inesperados para iniciar sessão.
   

3. Depois de terminar de adicionar a imagem corporativa, selecione **guardar**.

    Se este processo cria a configuração de imagem corporativa personalizada primeiro, se tornará o padrão para o seu inquilino. Se tiver configurações adicionais, poderá escolher a sua configuração predefinida.
    
    >[!Important]
    >Para adicionar mais configurações para o seu inquilino, de corporativa tem de escolher **nova linguagem** sobre o **Contoso - imagem corporativa da empresa** página. Esta ação abre o **configurar imagem corporativa da empresa** página, em que pode seguir os mesmos passos conforme apresentado acima.

## <a name="update-your-custom-branding"></a>Atualizar a imagem corporativa personalizada
Depois de criar a imagem corporativa personalizada, pode voltar atrás e alterar o que quiser.

### <a name="to-edit-your-custom-branding"></a>Para editar a sua marca personalizada
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**e, em seguida, selecione **imagem corporativa da empresa**e, em seguida, selecione **configurar**.

    ![Contoso - página de imagem corporativa da empresa, com a configuração predefinida apresentada](media/customize-branding/company-branding-default-config.png)

3. Sobre o **configurar imagem corporativa da empresa** página, adicionar, remover ou alterar qualquer uma das informações, com base nas descrições apresentadas na [personalizar a página de início de sessão do Azure AD](#customize-your-azure-ad-sign-in-page) seção deste artigo.

4. Selecione **Guardar**.

   Pode demorar até uma hora para que sejam apresentadas quaisquer alterações que tiver efetuado na imagem corporativa da página de início de sessão.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Adicionar uma imagem corporativa específica de idiomas ao seu diretório
Não é possível alterar o idioma de sua configuração original do seu idioma padrão. No entanto, se precisar de uma configuração num idioma diferente, pode criar uma nova configuração.

### <a name="to-add-a-language-specific-branding-configuration"></a>Para adicionar uma configuração de imagem corporativa específica do idioma

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**e, em seguida, selecione **imagem corporativa da empresa**e, em seguida, selecione **nova linguagem**.

    ![Contoso - página de imagem corporativa da empresa, com a nova opção de linguagem realçada](media/customize-branding/company-branding-new-language.png)

3. Sobre o **configurar imagem corporativa da empresa** página, selecione o seu idioma (por exemplo, francês) e, em seguida, adicione as suas informações de traduzido, com base nas descrições apresentadas na [personalizar a página de início de sessão do Azure AD](#customize-your-azure-ad-sign-in-page) seção deste artigo.

4. Selecione **Guardar**.

    O **Contoso – imagem corporativa da empresa** página atualizações para mostrar a nova configuração francês.

    ![Contoso - página de imagem corporativa da empresa, com a configuração predefinida apresentada](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Adicionar a sua marca personalizada a páginas
Adicionar a sua marca personalizada a páginas, modificando o final do URL com o texto, `?whr=yourdomainname`. Essa modificação funciona em várias páginas, incluindo a página de configuração de multi-factor Authentication (MFA), a página de configuração de reposição de palavra-passe Self-Service (SSPR) e o início de sessão na página.

**Exemplos:**

**Original URL:** https://aka.ms/MFASetup<br>
**URL personalizado:** https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com

**Original URL:** https://aka.ms/SSPR<br>
**URL personalizado:** https://passwordreset.microsoftonline.com/?whr=contoso.com

 
