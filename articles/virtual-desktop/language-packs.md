---
title: Instale pacotes de idiomas em VMs Windows 10 no Windows Virtual Desktop - Azure
description: Como instalar pacotes de idiomas para VMs multissessões do Windows 10 no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80634038"
---
# <a name="install-language-packs"></a>Instalar pacotes de idiomas

Quando configura as implementações do Windows Virtual Desktop internacionalmente, é uma boa ideia certificar-se de que a sua implementação suporta vários idiomas. Pode instalar pacotes de idiomas numa imagem de máquina virtual multi-sessão do Windows 10 Enterprise (VM) para suportar o maior número de idiomas que a sua organização necessitar. Este artigo dir-lhe-á como instalar pacotes de idiomas e capturar imagens que permitem aos seus utilizadores escolher os seus próprios idiomas de exibição.

Saiba mais sobre como implementar um VM em Azure na [Create a Windows virtual machine numa zona de disponibilidade com o portal Azure](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Este artigo aplica-se a VMs multissessões do Windows 10 Enterprise.

## <a name="install-a-language-pack"></a>Instale um pacote de idiomas

Para criar uma imagem VM com pacotes linguísticos, primeiro é necessário instalar pacotes linguísticos numa máquina e capturar uma imagem da mesmas.

Para instalar pacotes de idiomas:

1. Inscreva-se como administrador.
2. Certifique-se de que instalou todas as atualizações mais recentes do Windows e da Windows Store.
3. Vá ao tempo **de definições** > &**região****linguística.** > 
4. Em **País ou região,** selecione o seu país ou região preferido supor o menu suspenso.
    Neste exemplo, vamos selecionar a **França,** como mostra a seguinte imagem:

    ![Uma imagem da página da Região. A região atualmente selecionada é a França.](media/region-page-france.png)

5. Depois disso, selecione **Idioma**, em seguida, **selecione Adicionar um idioma**. Escolha o idioma que pretende instalar na lista e, em seguida, selecione **Next**.
6. Quando o **idioma Instalar apresentar** a janela aberta, selecione a caixa de verificação rotulada De instalar **o pacote de idiomas e definir como o meu idioma de exibição do Windows**.
7. Selecione **Instalar**.
8. Para adicionar várias línguas ao mesmo tempo, selecione **Adicionar uma língua,** em seguida, repita o processo para adicionar uma língua nos passos 5 e 6. Repita este processo para cada idioma que pretende instalar. No entanto, só pode definir uma língua como a sua linguagem de exibição de cada vez.

    Vamos passar por uma rápida demonstração visual. As imagens que se seguem mostram como instalar os pacotes de língua francesa e holandesa, em seguida, definir o francês como a língua de exibição.

    ![Uma imagem da página de Idioma no início do processo. O idioma de exibição do Windows selecionado é inglês.](media/language-page-default.png)

    ![Uma imagem da janela de seleção de idiomas. O utilizador inseriu o "francês" na barra de pesquisa para encontrar os pacotes em língua francesa.](media/select-language-french.png)

    ![Uma imagem da página de funcionalidades de idioma De instalação. O francês é selecionado como a língua preferida. As opções selecionadas são "Definir a minha linguagem de exibição", "Instalar o pacote de idiomas", "Reconhecimento da fala" e "Caligrafia".](media/install-language-features.png)

    Depois de instalados os seus pacotes linguísticos, deve ver os nomes dos seus pacotes linguísticos aparecerem na lista de idiomas.

    ![Uma imagem da página de idiomas com os novos pacotes de idiomas instalados. Os pacotes de línguafrancesa e holandesa estão listados em "línguas preferidas".](media/language-page-complete.png)

9. Se aparecer uma janela a pedir-lhe para assinar a sua sessão. Assine e depois inscreva-se novamente. A sua linguagem de exibição deve agora ser a língua selecionada.

10.  Ir ao **Relógio do Painel** > de Controlo e**região da região.****Clock and Region** > 

11.  Quando a janela **Região** abrir, selecione o separador **'Administração'** e, em seguida, selecione **as definições de Cópia**.

12.  Selecione as caixas de verificação rotuladas de **ecrã de boas-vindas e contas** de sistema e novas contas de **utilizador**.

13.  Selecione **OK**.

14.  Uma janela abre-se e diz-lhe para reiniciar a sessão. Selecione **Reiniciar agora**.

15.  Depois de ter assinado, volte ao **Control Panel** > **Clock and Region** > **Region Region**.

16.  Selecione o separador **Administração.**

17.  Selecione **Local do sistema Dealterar**.

18. No menu suspenso no **local do sistema atual,** selecione a linguagem local que pretende utilizar. Depois disso, selecione **OK**.

19. Selecione **Reiniciar agora** para reiniciar a sua sessão mais uma vez.

Parabéns, instalou os seus pacotes linguísticos!

Antes de continuar, certifique-se de que o seu sistema tem as versões mais recentes da loja Windows e Windows instaladas.

## <a name="sysprep"></a>Sysprep

Em seguida, é necessário preparar a sua máquina para a preparar para o processo de captação de imagem.

Para sipreparar a sua máquina:

1. Abra o PowerShell como Administrador.
2. Executar o seguinte cmdlet para ir ao diretório correto:
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Em seguida, executar o seguinte cmdlet:
    
    ```powershell
    .\sysprep.exe
    ```

4. Quando a janela da ferramenta de preparação do sistema abrir, selecione a caixa de verificação com a etiqueta **Generalize,** em seguida, vá para **as Opções** de Encerramento e selecione **Desligar** a partir do menu suspenso.

>[!NOTE]
>O processo de syprep levará alguns minutos para terminar. À medida que o VM se desliga, a sua sessão remota desligar-se-á.

### <a name="resolve-sysprep-errors"></a>Resolver erros de sysprep

Se vir uma mensagem de erro durante o processo de sysprep, eis o que deve fazer:

1. Abra a **unidade C** e vá para **o Windows** > **System32 Sysprep** > **Panther**e abra o ficheiro **setuperr.**

   O texto no ficheiro de erro dir-lhe-á que precisa de desinstalar um pacote de idiomas específico, como mostra a imagem seguinte. Copie o nome do pacote linguístico para o próximo passo.

   ![Uma imagem do ficheiro setuperr. O texto com o nome do pacote é realçado em azul escuro.](media/setuperr-package-name.png)

2. Abra uma nova janela PowerShell e execute o seguinte cmdlet com o nome de pacote copiado no passo 2 para remover o pacote de idiomas:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Verifique se removeu a embalagem executando novamente o `Remove-AppxPackage` cmdlet. Se removeu com sucesso o pacote, deve ver uma mensagem que diz que o pacote que está a tentar remover não está lá.

4. Volte `sysprep.exe` a correr o cmdlet.

## <a name="capture-the-image"></a>Capture a imagem

Agora que o seu sistema está pronto, pode capturar uma imagem para que outros utilizadores possam começar a utilizar VMs com base no seu sistema sem ter de repetir o processo de configuração.

Para capturar uma imagem:

1. Vá ao portal Azure e selecione o nome da máquina configurada em Instalar um pacote de [idiomas](#install-a-language-pack) e [sysprep](#sysprep).

2. Selecione **Capturar**.

3. Introduza um nome para a sua imagem no campo **Nome** e atribua-a ao grupo de recursos utilizando o menu de entrega do **grupo Resource,** como mostra a imagem seguinte.

   ![Uma imagem da janela de imagem Criar. O nome que o utilizador deu a esta imagem de teste é "vmwvd-image-fr", e atribuíram-na ao grupo de recursos "testwvdimagerg".](media/create-image.png)

4. Selecione **Criar**.

5. Espere alguns minutos para que o processo de captura termine. Quando a imagem estiver pronta, deve ver uma mensagem no Centro de Notificações a informar que a imagem foi captada.

Agora pode implementar um VM usando a sua nova imagem. Quando implementar o VM, certifique-se de seguir as instruções em [Criar uma máquina virtual Windows numa zona de disponibilidade com o portal Azure](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Como alterar o idioma do display para utilizadores padrão

Os utilizadores standard podem alterar o idioma de exibição nos seus VMs.

Para alterar a linguagem de exibição:

1. Ir para definições de **idioma.** Se não sabe onde está, pode introduzir o **Idioma** na barra de pesquisa no Menu Iniciar.

2. No menu de exibição do Windows, selecione o idioma que pretende utilizar como idioma de exibição.

3. Assine a sua sessão e volte a entrar. A linguagem de exibição deve agora ser a que selecionou no passo 2.
