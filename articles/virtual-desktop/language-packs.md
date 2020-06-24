---
title: Instale pacotes de idiomas no Windows 10 VMs no Windows Virtual Desktop - Azure
description: Como instalar pacotes de idiomas para VMs multi-sessão do Windows 10 no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7d05c1222ff43537ab8dad34588b80fe537fd0a1
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85200414"
---
# <a name="install-language-packs"></a>Instalar pacotes de idiomas

Quando configurar as implementações do Windows Virtual Desktop a nível internacional, é uma boa ideia certificar-se de que a sua implementação suporta vários idiomas. Pode instalar pacotes de idiomas numa imagem de máquina virtual (VM) multi-sessão do Windows 10 Enterprise para suportar o número de idiomas que a sua organização necessita. Este artigo irá dizer-lhe como instalar pacotes de idiomas e capturar imagens que permitem que os seus utilizadores escolham os seus próprios idiomas de exibição.

Saiba mais sobre como implementar um VM em Azure na [Create a windows virtual machine em uma zona de disponibilidade com o portal Azure](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Este artigo aplica-se aos VMs multi-sessão do Windows 10 Enterprise.

## <a name="install-a-language-pack"></a>Instale um pacote de idiomas

Para criar uma imagem VM com pacotes de idiomas, primeiro é necessário instalar pacotes de linguagem numa máquina e capturar uma imagem dela.

Para instalar pacotes de idiomas:

1. Inscreva-se como administrador.
2. Certifique-se de que instalou todas as atualizações mais recentes do Windows e windows Store.
3. Ir para **a Hora das Definições**&  >  **Time & Language**  >  **Região**da Língua .
4. Em **País ou região,** selecione o seu país ou região preferido a partir do menu suspenso.
    Neste exemplo, vamos selecionar a **França,** como mostra a seguinte imagem:

    ![Uma imagem da página da Região. A região atualmente selecionada é a França.](media/region-page-france.png)

5. Depois disso, selecione **Linguagem**e, em seguida, selecione **Adicione um idioma**. Escolha o idioma que pretende instalar na lista e, em seguida, selecione **Seguinte**.
6. Quando a janela **de instalação de recursos** de instalação se abrir, selecione a caixa de verificação rotulada Para instalar o pacote de **idiomas e definir como o meu idioma de exibição do Windows**.
7. Selecione **Instalar**.
8. Para adicionar vários idiomas ao mesmo tempo, **selecione Adicione um idioma,** em seguida, repita o processo para adicionar um idioma nos passos 5 e 6. Repita este processo para cada idioma que pretende instalar. No entanto, só pode definir um idioma como o seu idioma de exibição de cada vez.

    Vamos passar por uma demonstração visual rápida. As imagens que se seguem mostram como instalar os pacotes de língua francesa e holandesa, em seguida, definir o francês como a língua de exibição.

    ![Uma imagem da página idioma no início do processo. O idioma de exibição do Windows selecionado é inglês.](media/language-page-default.png)

    ![Uma imagem da janela de seleção de idiomas. O utilizador inseriu "francês" na barra de pesquisa para encontrar os pacotes em língua francesa.](media/select-language-french.png)

    ![Uma imagem da página de funcionalidades do idioma de instalação. O francês é selecionado como a língua preferida. As opções selecionadas são "Definir o meu idioma de exibição", "Instalar o pacote de linguagem", "Reconhecimento de voz" e "Caligrafia".](media/install-language-features.png)

    Depois de os seus pacotes linguísticos terem sido instalados, deve ver os nomes dos seus pacotes linguísticos na lista de idiomas.

    ![Uma imagem da página do idioma com os novos pacotes de idiomas instalados. Os pacotes de línguas francesa e neerlandesa estão listados em "línguas preferenciais".](media/language-page-complete.png)

9. Se aparecer uma janela a pedir-lhe para assinar fora da sessão. Assine e depois inscreva-se de novo. O seu idioma de exibição deve agora ser o idioma que selecionou.

10.  Vá ao **Painel de Controlo**relógio e  >  **Clock and Region**  >  **região da região.**

11.  Quando a janela **da Região** abrir, selecione o separador **Administração** e, em seguida, selecione **definições de Cópia**.

12.  Selecione as caixas de verificação com a etiqueta **De ecrã de boas-vindas e contas do sistema** e novas contas de **utilizador.**

13.  Selecione **OK**.

14.  Uma janela abrir-se-á e dir-lhe-á para reiniciar a sessão. **Selecione Reiniciar agora**.

15.  Depois de ter assinado, volte para o **Painel de Controlo**do Relógio e  >  **Clock and Region**  >  **Região.**

16.  Selecione o separador **Administração.**

17.  Selecione **Alterar local do sistema**local .

18. No menu suspenso em **Local do sistema Current,** selecione o idioma local que pretende utilizar. Depois disso, selecione **OK**.

19. **Selecione Reiniciar agora** para reiniciar a sessão novamente.

Parabéns, instalaste os teus pacotes de idiomas!

Antes de continuar, certifique-se de que o seu sistema tem as versões mais recentes da loja Windows e Windows instaladas.

## <a name="sysprep"></a>Sysprep

Em seguida, precisa de sysprepp a sua máquina para prepará-la para o processo de captura de imagens.

Para sysprep a sua máquina:

1. Abra o PowerShell como Administrador.
2. Executar o seguinte cmdlet para ir para o diretório correto:

    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Em seguida, executar o seguinte cmdlet:

    ```powershell
    .\sysprep.exe
    ```

4. Quando a janela ferramenta de preparação do sistema abrir, selecione a caixa de verificação marcada **generalize,** em seguida, vá para **opções de encerramento** e selecione **Desligue** do menu suspenso.

>[!NOTE]
>O processo de syprep levará alguns minutos para terminar. À medida que o VM se desliga, a sua sessão remota desligar-se-á.

### <a name="resolve-sysprep-errors"></a>Resolver erros de sysprep

Se vir uma mensagem de erro durante o processo sysprep, eis o que deve fazer:

1. Abra **a Unidade C** e vá ao **Windows**  >  **System32 Sysprep**  >  **Panther**e, em seguida, abra o ficheiro **configurador.**

   O texto no ficheiro de erro dir-lhe-á que precisa de desinstalar um pacote linguístico específico, como mostra a seguinte imagem. Copie o nome do pacote linguístico para o próximo passo.

   ![Uma imagem do ficheiro configurador. O texto com o nome do pacote é realçado em azul escuro.](media/setuperr-package-name.png)

2. Abra uma nova janela PowerShell e execute o seguinte cmdlet com o nome do pacote copiado no passo 2 para remover o pacote linguístico:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Verifique se removeu a embalagem executando novamente o `Remove-AppxPackage` cmdlet. Se removeu o pacote com sucesso, deve ver uma mensagem que diz que o pacote que está a tentar remover não está lá.

4. Volte a executar o `sysprep.exe` cmdlet.

## <a name="capture-the-image"></a>Capturar a imagem

Agora que o seu sistema está pronto, pode capturar uma imagem para que outros utilizadores possam começar a usar VMs com base no seu sistema sem ter de repetir o processo de configuração.

Para capturar uma imagem:

1. Vá ao portal Azure e selecione o nome da máquina que configuraste na [Instalação de um pacote de idiomas](#install-a-language-pack) e [sysprep](#sysprep).

2. Selecione **Capture**.

3. Introduza um nome para a sua imagem no campo **Nome** e atribua-o ao grupo de recursos utilizando o menu suspenso do **grupo De recurso,** como mostra a imagem seguinte.

   ![Uma imagem da janela de imagem Create. O nome que o utilizador deu a esta imagem de teste é "vmwvd-image-fr", e atribuíram-no ao grupo de recursos "testwvdimagerg".](media/create-image.png)

4. Selecione **Criar**.

5. Aguarde alguns minutos para o processo de captura terminar. Quando a imagem estiver pronta, deverá ver uma mensagem no Centro de Notificações, informando-o de que a imagem foi captada.

Agora pode implementar um VM utilizando a sua nova imagem. Quando implementar o VM, certifique-se de seguir as instruções na [Criar uma máquina virtual Windows numa zona de disponibilidade com o portal Azure](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Como alterar o idioma de exibição para utilizadores padrão

Os utilizadores padrão podem alterar o idioma de exibição nos seus VMs.

Para alterar o idioma de exibição:

1. Ir para **definições de idiomas**. Se não souber onde está, pode introduzir o **Idioma** na barra de pesquisa no Menu Iniciar.

2. No menu drop-down do idioma do windows, selecione o idioma que pretende utilizar como idioma de exibição.

3. Assine fora da sua sessão e, em seguida, inscreva-se de volta. O idioma de exibição deve ser agora o que selecionou no passo 2.
