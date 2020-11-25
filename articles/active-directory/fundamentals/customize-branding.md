---
title: Adicione marca à página de assinatura da sua organização - Azure AD
description: Instruções sobre como adicionar a marca da sua organização à página de inscrição do Diretório Ativo Azure.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/24/2020
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb138ade0f579a7545c0910646b6adfb7d5ac02a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996666"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Adicione marca à página de inscrição do Azure Ative Directory da sua organização
Use o logótipo da sua organização e esquemas de cores personalizados para fornecer um look-and-feel consistente nas suas páginas de inscrição do Azure Ative(Azure AD). As suas páginas de insusição aparecem quando os utilizadores acedem às aplicações web da sua organização, como o Microsoft 365, que utiliza o Azure AD como fornecedor de identidade.

>[!NOTE]
>A adição de marca personalizada requer que tenha licenças Azure Ative Directory Premium 1 ou Premium 2. Para obter mais informações sobre licenciamento e edições, consulte [Inscreva-se no Azure AD Premium.](active-directory-get-started-premium.md)<br><br>As edições Azure AD Premium estão disponíveis para clientes na China usando o exemplo mundial do Azure Ative Directory. As edições Azure AD Premium não são atualmente suportadas no serviço Azure operado pela 21Vianet na China. Para obter mais informações, contacte-nos através do [Fórum do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Personalize a sua página de ad AD Azure
Pode personalizar as suas páginas de inscrição AD AZure, que aparecem quando os utilizadores insinam-se nas aplicações específicas para o inquilino da sua organização, tais `https://outlook.com/contoso.com` como, ou ao passar uma variável de domínio, como `https://passwordreset.microsoftonline.com/?whr=contoso.com` .

A sua marca personalizada não aparecerá imediatamente quando os seus utilizadores se derem a sites como, em www \. office.com. Em vez disso, o utilizador tem de iniciar sing-in antes da sua marca personalizada aparecer. Depois de o utilizador ter assinado, a marca pode demorar 15 minutos ou mais a aparecer. 

> [!NOTE]
> Todos os elementos de marca são opcionais. Por exemplo, se especificar um logótipo de banner sem imagem de fundo, a página de início mostrará o seu logotipo com uma imagem de fundo padrão do site de destino (por exemplo, Microsoft 365).<br><br>Além disso, a marca de página de inscrição não é entregue a contas pessoais da Microsoft. Se os seus utilizadores ou clientes empresariais iniciarem saúde através de uma conta pessoal da Microsoft, a página de inscrição não refletirá a marca da sua organização.

### <a name="to-customize-your-branding"></a>Para personalizar a sua marca
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**, e, em seguida, selecione **a marca da Empresa**, e, em seguida, selecione **Configure**.

    ![Contoso - Página de marca da empresa, opção de configuração em destaque](media/customize-branding/company-branding-configure-button.png)

3. Na página **de marca Configure da empresa,** forneça qualquer ou todas as seguintes informações.

    >[!IMPORTANT]
    >Todas as imagens personalizadas que adicionar nesta página têm tamanho de imagem (pixels) e potencialmente tamanho de ficheiro (KB), restrições. Devido a estas restrições, provavelmente terá de usar um editor de fotografia para criar as imagens do tamanho certo.

    - **Definições gerais**

        ![Configurar a página de marca da empresa, com configurações gerais concluídas](media/customize-branding/configure-company-branding-general-settings.png)

        - **Idioma.** O idioma é automaticamente definido como o seu padrão e não pode ser alterado.
        
        - **Imagem de fundo de página de iniciar s-in.** Selecione um ficheiro de imagem .png ou .jpg para aparecer como pano de fundo para as suas páginas de inscrição. A imagem será ancorada ao centro do navegador, e escalará para o tamanho do espaço visível. Não é possível selecionar uma imagem maior do que 1920x1080 pixels de tamanho ou que tenha um tamanho de ficheiro superior a 300 KB.
        
            Recomenda-se a utilização de imagens sem um forte foco de assunto, por exemplo, uma caixa branca opaca aparece no centro do ecrã, e pode cobrir qualquer parte da imagem dependendo das dimensões do espaço visível.

        - **Logotipo do banner.** Selecione uma versão .png ou .jpg do seu logotipo para aparecer na página de início de s início depois de o utilizador introduzir um nome de utilizador e na página do portal **My Apps.**
            
            A imagem não pode ser mais alta que 60 pixels ou mais de 280 pixels. Recomendamos a utilização de uma imagem transparente, uma vez que o fundo pode não corresponder ao fundo do seu logotipo. Também recomendamos que não adicione estofos em torno da imagem ou pode fazer o seu logotipo parecer pequeno.

        - **Dica de nome de utilizador.** Digite o texto sugestiva que aparece para os utilizadores se esquecer o seu nome de utilizador. Este texto deve ser Unicode, sem links ou código, e não pode exceder 64 caracteres. Se os hóspedes iniciarem sôms na sua aplicação, sugerimos que não adicione esta dica.

        - **Iniciar sessão de texto e formatação.** Digite o texto que aparece na parte inferior da página de inscrição. Pode utilizar este texto para comunicar informações adicionais, como o número de telefone para o seu balcão de assistência ou uma declaração legal. Este texto deve ser Unicode e não exceder 1024 caracteres.

           Pode personalizar o texto da página de entrada que introduziu. Para iniciar um novo parágrafo, utilize a tecla de entrada duas vezes. Também pode alterar a formatação de texto para incluir um link arrojado, itálico, um ponto de sublinhar ou clicável. Utilize a seguinte sintaxe para adicionar formatação ao texto: 

          > Hiperligação: ```[text](link)``` 
          
          > Arrojado: ``` **text** ``` ou ``` __text__ ``` 
          
          > Itálico: ``` *text* ``` ou ``` _text_ ``` 
          
          > Sublinhar: ``` ++text++ ``` 

    - **Definições avançadas**
            
        ![Configurar a página de marca da empresa, com configurações avançadas concluídas](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Cor de fundo da página de iniciar s-in.** Especifique a cor hexadecimal (por exemplo, o branco é #FFFFFF) que aparecerá no lugar da sua imagem de fundo em situações de ligação de baixa largura de banda. Recomendamos a utilização da cor primária do seu logotipo do banner ou da cor da sua organização.

        - **Imagem de logotipo quadrado.** Selecione uma imagem .png (preferencial) ou .jpg do logótipo da sua organização para aparecer aos utilizadores durante o processo de configuração de novos dispositivos Do Windows 10 Enterprise. Esta imagem é utilizada apenas para autenticação do Windows e aparece apenas em inquilinos que estão a utilizar [o Windows Autopilot]( /windows/deployment/windows-autopilot/windows-10-autopilot) para implementação ou para páginas de entrada de palavra-passe noutras experiências do Windows 10. Em alguns casos, pode também aparecer no diálogo de consentimento.
        
            A imagem não pode ser maior do que 240x240 pixels de tamanho e deve ter um tamanho de arquivo inferior a 10 KB. Recomendamos a utilização de uma imagem transparente, uma vez que o fundo pode não corresponder ao fundo do seu logotipo. Também recomendamos que não adicione estofos em torno da imagem ou pode fazer o seu logotipo parecer pequeno.
    
        - **Imagem de logotipo quadrado, tema escuro.** O mesmo que a imagem do logotipo quadrado acima. Esta imagem de logotipo ocupa o lugar da imagem do logotipo quadrado quando usada com um fundo escuro, como com o Windows 10 Azure AD ligado aos ecrãs durante a experiência fora da caixa (OOBE).  Se o seu logótipo fica bem em fundos brancos, azuis escuros e pretos, não precisa adicionar esta imagem. 
        
        - **Mostrar a opção de permanecer assinado.** Pode optar por deixar que os seus utilizadores permaneçam inscritos no Azure AD até que seja explicitamente assinado. Se escolher **Não,** esta opção está escondida e os utilizadores devem iniciar sação sempre que o navegador estiver fechado e reaberto.

            Esta capacidade só está disponível no objeto de marca padrão e não em qualquer objeto específico da linguagem. Para saber mais sobre configurar e resolver problemas a opção de permanecer inscrito, consulte [o pedido de 'Permanência assinada?' para as contas AD do Azure](keep-me-signed-in.md)
        
            >[!NOTE]
            >Algumas funcionalidades do SharePoint Online e do Office 2010 dependem da capacidade de os utilizadores poderem escolher manter a sessão iniciada. Se tiver definido esta opção como **Não**, os utilizadores poderão ver avisos adicionais e inesperados para iniciar sessão.
   

3. Depois de terminar de adicionar a sua marca, **selecione Save**.

    Se este processo criar a sua primeira configuração de marca personalizada, torna-se o padrão para o seu inquilino. Se tiver configurações adicionais, poderá escolher a sua configuração padrão.
    
    >[!IMPORTANT]
    >Para adicionar mais configurações de marca corporativa ao seu inquilino, deve escolher **Nova linguagem** na página de **marca Contoso - Empresa.** Isto abre a página de **marca Configure,** onde pode seguir os mesmos passos acima.

## <a name="update-your-custom-branding"></a>Atualize a sua marca personalizada
Depois de criar a sua marca personalizada, pode voltar e mudar o que quiser.

### <a name="to-edit-your-custom-branding"></a>Para editar a sua marca personalizada
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**, e, em seguida, selecione **a marca da Empresa**, e, em seguida, selecione **Configure**.

    ![Contoso - Página de marca da empresa, com configuração padrão mostrada](media/customize-branding/company-branding-default-config.png)

3. Na página **de marca Configure da empresa,** adicione, remova ou altere qualquer informação, com base nas descrições na secção de [inscrição AD do Azure](#customize-your-azure-ad-sign-in-page) deste artigo.

4. Selecione **Guardar**.

   Pode demorar até uma hora para que sejam apresentadas quaisquer alterações que tiver efetuado na imagem corporativa da página de início de sessão.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Adicionar uma imagem corporativa específica de idiomas ao seu diretório
Não é possível alterar o idioma da sua configuração original a partir do seu idioma predefinido. No entanto, se precisar de uma configuração num idioma diferente, pode criar uma nova configuração.

### <a name="to-add-a-language-specific-branding-configuration"></a>Para adicionar uma configuração de marca específica para a linguagem

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**, e, em seguida, selecione **a marca da Empresa**, e, em seguida, selecione Novo **idioma**.

    ![Contoso - Página de marca da empresa, com nova opção linguística em destaque](media/customize-branding/company-branding-new-language.png)

3. Na página de **marca Configure,** selecione o seu idioma (por exemplo, francês) e adicione as suas informações traduzidas, com base nas descrições na secção de [inscrição AD do Azure](#customize-your-azure-ad-sign-in-page) deste artigo.

4. Selecione **Guardar**.

    As atualizações da página **de marca Contoso – Empresa** para mostrar a sua nova configuração francesa.

    ![Contoso - Página de marca da empresa, com a nova configuração linguística mostrada](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Adicione o seu branding personalizado às páginas
Adicione a sua marca personalizada às páginas modificando o final do URL com o texto, `?whr=yourdomainname` . Esta modificação funciona em várias páginas, incluindo a página de configuração de autenticação multi-factor (MFA), a página de configuração de Reset de Palavra-Passe de Autosserviço (SSPR) e o sinal na página.

**Exemplos:**

**URL original:**https://aka.ms/MFASetup<br>
**URL personalizado:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**URL original:**https://aka.ms/SSPR<br>
**URL personalizado:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`
