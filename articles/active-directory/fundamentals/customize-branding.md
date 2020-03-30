---
title: Adicione branding à página de entrada da sua organização - Azure AD
description: Instruções sobre como adicionar a marca da sua organização à página de entrada do Diretório Ativo Azure.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441fdd14cc2c734b6ce532f3ad1d30663b2f56c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049800"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Adicione branding à página de inscrição do Azure Ative Directory da sua organização
Utilize o logótipo da sua organização e esquemas de cores personalizadas para fornecer uma aparência e sensação consistentes nas suas páginas de entrada de login Azure Ative (Azure AD). As suas páginas de entrada aparecem quando os utilizadores entram nas aplicações baseadas na web da sua organização, como o Office 365, que utiliza o Azure AD como fornecedor de identidade.

>[!Note]
>A adição de marca personalizada requer que utilize o Azure Ative Directory Premium 1, Premium 2 ou Basic edições, ou para ter uma licença do Office 365. Para mais informações sobre licenciamento e edições, consulte [Signup para Azure AD Premium](active-directory-get-started-premium.md).<br><br>As edições Premium e Básica do Azure AD estão disponíveis para clientes na China que utilizem a instância mundial do Azure Active Directory. As edições do Azure AD Premium e Básico não são atualmente suportadas pelo serviço do Azure operado pela 21Vianet na China. Para obter mais informações, contacte-nos através do [Fórum do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Personalize a sua página de entrada de anúncios azure AD
Pode personalizar as suas páginas de entrada de Anúncios Azure, que aparecem quando os `https://outlook.com/contoso.com`utilizadores assinam nas aplicações `https://passwordreset.microsoftonline.com/?whr=contoso.com`específicas do inquilino da sua organização, tais como , ou quando passam uma variável de domínio, como .

A sua marca personalizada não aparecerá imediatamente quando os\.seus utilizadores forem a sites como, www office.com. Em vez disso, o utilizador tem de iniciar sessão antes da sua marca personalizada aparecer. Depois de o utilizador ter assinado, a marca pode demorar 15 minutos ou mais a aparecer. 

> [!NOTE]
> Todos os elementos de marca são opcionais. Por exemplo, se especificar um logótipo de banner sem imagem de fundo, a página de inscrição mostrará o seu logotipo com uma imagem de fundo padrão do site de destino (por exemplo, Office 365).<br><br>Além disso, o branding de página de iniciar sessão não é transversal a contas pessoais da Microsoft. Se os seus utilizadores ou hóspedes assinarem através de uma conta pessoal da Microsoft, a página de sessão não refletirá a marca da sua organização.

### <a name="to-customize-your-branding"></a>Para personalizar a sua marca
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**, e, em seguida, selecione **a marca da Empresa,** e, em seguida, selecione **Configure**.

    ![Contoso - Página de branding da empresa, opção configure em destaque](media/customize-branding/company-branding-configure-button.png)

3. Na página de branding da **empresa Configure,** forneça todas ou todas as seguintes informações.

    >[!Important]
    >Todas as imagens personalizadas que adiciona nesta página têm tamanho de imagem (pixels) e potencialmente tamanho de ficheiro (KB), restrições. Devido a estas restrições, é provável que precise de usar um editor de fotografia para criar as imagens do tamanho certo.

    - **Definições gerais**

        ![Configure a página de branding da empresa, com as configurações gerais concluídas](media/customize-branding/configure-company-branding-general-settings.png)

        - **A linguagem.** O idioma é automaticamente definido como o seu padrão e não pode ser alterado.
        
        - **Imagem de fundo de página de iniciar sessão.** Selecione um ficheiro de imagem .png ou .jpg para aparecer como pano de fundo para as suas páginas de inscrição. 
        
            A imagem não pode ser maior do que 1920x1080 pixels de tamanho e deve ter um tamanho de arquivo inferior a 300 KB.

        - **Logotipo do banner.** Selecione uma versão .png ou .jpg do seu logótipo para aparecer na página de entrada após o utilizador introduzir um nome de utilizador e na página do portal **My Apps.**
            
            A imagem não pode ser superior a 60 pixels ou mais do que 280 pixels. Recomendamos a utilização de uma imagem transparente, uma vez que o fundo pode não corresponder ao fundo do logotipo. Também recomendamos não adicionar estofos em torno da imagem ou pode fazer o seu logotipo parecer pequeno.

        - **Sugestão de nome de utilizador.** Digite o texto de dica que aparece aos utilizadores se esquecerem o seu nome de utilizador. Este texto deve ser Unicode, sem links ou código, e não pode exceder 64 caracteres. Se os hóspedes assinarem na sua aplicação, sugerimos que não adicione esta dica.

        - **Sessão de texto de página.** Digite o texto que aparece na parte inferior da página de inscrição. Pode utilizar este texto para comunicar informações adicionais, como o número de telefone para o seu balcão de ajuda ou uma declaração legal. Este texto deve ser Unicode e não exceder 256 caracteres. Também sugerimos não incluir links ou tags HTML.

    - **Definições avançadas**
            
        ![Configure página de branding da empresa, com configurações avançadas concluídas](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Cor de fundo de página de inscrição.** Especifique a cor hexadecimal (por exemplo, o branco é #FFFFFF) que aparecerá no lugar da sua imagem de fundo em situações de ligação de baixa largura de banda. Recomendamos a utilização da cor primária do seu logotipo do banner ou da sua cor de organização.

        - **Imagem de logotipo quadrado.** Selecione uma imagem .png (preferida) ou .jpg do logótipo da sua organização para aparecer aos utilizadores durante o processo de configuração para novos dispositivos Windows 10 Enterprise. Esta imagem é usada apenas para autenticação do Windows e aparece apenas em inquilinos que estão a utilizar o [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) para implementação ou para páginas de entrada de palavras-passe noutras experiências do Windows 10. Em alguns casos, também pode aparecer no diálogo de consentimento.
        
            A imagem não pode ser maior do que 240x240 pixels de tamanho e deve ter um tamanho de ficheiro inferior a 10 KB. Recomendamos a utilização de uma imagem transparente, uma vez que o fundo pode não corresponder ao fundo do logotipo. Também recomendamos não adicionar estofos em torno da imagem ou pode fazer o seu logotipo parecer pequeno.
    
        - **Imagem de logotipo quadrado, tema escuro.** O mesmo que a imagem do logotipo quadrado acima. Esta imagem do logotipo toma o lugar da imagem do logotipo quadrado quando usada com um fundo escuro, como com o Windows 10 Azure AD juntou ecrãs durante a experiência fora da caixa (OOBE).  Se o seu logótipo fica bem em fundos brancos, azuis escuros e pretos, não precisa de adicionar esta imagem. 
        
        - **Mostrar opção para permanecer inscrito.** Pode optar por deixar os seus utilizadores permanecerem inscritos no Azure AD até assinarem explicitamente. Se escolher **O ,** esta opção está escondida e os utilizadores devem iniciar sessão sempre que o navegador estiver fechado e reaberto.
        
            >[!Note]
            >Algumas funcionalidades do SharePoint Online e do Office 2010 dependem da capacidade de os utilizadores poderem escolher manter a sessão iniciada. Se tiver definido esta opção como **Não**, os utilizadores poderão ver avisos adicionais e inesperados para iniciar sessão.
   

3. Depois de terminar de adicionar a sua marca, selecione **Save**.

    Se este processo criar a sua primeira configuração de marca personalizada, torna-se o padrão para o seu inquilino. Se tiver configurações adicionais, poderá escolher a sua configuração predefinida.
    
    >[!Important]
    >Para adicionar mais configurações de marca corporativa ao seu inquilino, você deve escolher **Nova linguagem** na página de **marca Contoso - Empresa.** Isto abre a página de branding da **empresa Configure,** onde pode seguir os mesmos passos que acima.

## <a name="update-your-custom-branding"></a>Atualize a sua marca personalizada
Depois de criar a sua marca personalizada, pode voltar e mudar o que quiser.

### <a name="to-edit-your-custom-branding"></a>Para editar a sua marca personalizada
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**, e, em seguida, selecione **a marca da Empresa,** e, em seguida, selecione **Configure**.

    ![Contoso - Página de branding da empresa, com configuração padrão mostrada](media/customize-branding/company-branding-default-config.png)

3. Na página de marca da **empresa Configure,** adicione, remova ou altere qualquer informação, com base nas descrições na secção de entrada de anúncios da Empresa [Azure](#customize-your-azure-ad-sign-in-page) deste artigo.

4. Selecione **Guardar**.

   Pode demorar até uma hora para que sejam apresentadas quaisquer alterações que tiver efetuado na imagem corporativa da página de início de sessão.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Adicionar uma imagem corporativa específica de idiomas ao seu diretório
Não pode alterar o idioma da configuração original a partir do seu idioma padrão. No entanto, se precisar de uma configuração num idioma diferente, pode criar uma nova configuração.

### <a name="to-add-a-language-specific-branding-configuration"></a>Para adicionar uma configuração de marca específica do idioma

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**, e, em seguida, selecione **a marca da Empresa,** e, em seguida, selecione **New language**.

    ![Contoso - Página de branding da empresa, com nova opção linguística em destaque](media/customize-branding/company-branding-new-language.png)

3. Na página de branding da **empresa Configure,** selecione o seu idioma (por exemplo, francês) e adicione as suas informações traduzidas, com base nas descrições na secção de entrada de sinais do Seu Anúncio [Azure](#customize-your-azure-ad-sign-in-page) deste artigo.

4. Selecione **Guardar**.

    As atualizações da página de **marca Contoso – Empresa** para mostrar a sua nova configuração francesa.

    ![Contoso - Página de branding da empresa, com configuração padrão mostrada](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Adicione a sua marca personalizada às páginas
Adicione a sua marca personalizada às páginas modificando a `?whr=yourdomainname`extremidade do URL com o texto, . Esta modificação funciona em várias páginas, incluindo a página de configuração de autenticação multi-factor (MFA), a página de configuração de reset de palavra-passe de autosserviço (SSPR) e o sinal na página.

**Exemplos:**

**URL original:**https://aka.ms/MFASetup<br>
**URL personalizado:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**URL original:**https://aka.ms/SSPR<br>
**URL personalizado:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`

 
