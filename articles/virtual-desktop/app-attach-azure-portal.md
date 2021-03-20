---
title: Windows Virtual Desktop MSIX app anexar pré-visualização do portal - Azure
description: Como configurar o anexo de aplicações MSIX para o Windows Virtual Desktop utilizando o portal Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 02/11/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: c775d81b88c891d6d8ea0a4597b4fa4fee29c86a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737550"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Configurar a anexação de aplicações MSIX com o portal do Azure

> [!IMPORTANT]
> O anexo de aplicações MSIX encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo irá acompanhá-lo sobre como configurar o anexo de aplicações MSIX (pré-visualização) num ambiente de Ambiente de Trabalho Virtual do Windows.

## <a name="requirements"></a>Requisitos

>[!IMPORTANT]
>Antes de começar, certifique-se de preencher e submeter [este formulário](https://aka.ms/enablemsixappattach) para permitir que a app MSIX se anexe na sua subscrição. Se não tiver um pedido aprovado, o anexo de aplicações MSIX não funcionará. A aprovação dos pedidos pode demorar até 24 horas durante os dias úteis. Receberá um e-mail quando o seu pedido tiver sido aceite e concluído.

Aqui está o que precisa para configurar o anexo da app MSIX:

- Uma implementação de ambiente de trabalho virtual do Windows em funcionamento. Para aprender a implementar o Windows Virtual Desktop (clássico), consulte [Criar um inquilino no Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Para aprender a implementar o Windows Virtual Desktop com integração do Azure Resource Manager, consulte [Criar uma piscina de anfitriões com o portal Azure](./create-host-pools-azure-marketplace.md).
- Uma piscina de anfitriões virtual do Windows Desktop com pelo menos um anfitrião de sessão ativa.
- Esta piscina de anfitriões deve estar no ambiente de validação. 
- A ferramenta de embalagem MSIX.
- Uma aplicação embalada pela MSIX expandiu-se para uma imagem MSIX que é enviada para uma partilha de ficheiros.
- Uma partilha de ficheiros na sua implementação virtual do Windows Desktop onde o pacote MSIX será armazenado.
- A partilha de ficheiros onde fez o upload da imagem MSIX também deve estar acessível a todas as máquinas virtuais (VMs) na piscina anfitriã. Os utilizadores precisarão de permissões apenas de leitura para aceder à imagem.
- Se o certificado não for de confiança pública, siga as instruções nos [certificados de instalação](app-attach.md#install-certificates).

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Desativar atualizações automáticas para aplicações de app MSIX anexar

Antes de começar, tem de desativar as atualizações automáticas para aplicações de anexação de aplicações MSIX. Para desativar as atualizações automáticas, terá de executar os seguintes comandos numa solicitação de comando elevada:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

>[!NOTE]
>Recomendamos que reinicie a máquina virtual depois de ativar o Hiper-V.

## <a name="configure-the-msix-app-attach-management-interface"></a>Configure a interface de gestão de app MSIX

Em seguida, terá de descarregar e configurar a interface de gestão anexada à app MSIX para o portal Azure.

Para configurar a interface de gestão:

1. [Abra o portal Azure](https://portal.azure.com).
2. Se receber uma pergunta a perguntar se considera a extensão de confiança, **selecione Permitir**.

      > [!div class="mx-imgBorder"]
      > ![Uma imagem da janela de extensões não fidedinhas. "Permitir" é destacado a vermelho.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Adicione uma imagem MSIX à piscina anfitriã

Em seguida, você precisará adicionar a imagem MSIX à sua piscina de anfitrião.

Para adicionar a imagem MSIX:

1. Abra o portal do Azure.

2. Introduza o **Ambiente de Trabalho Virtual do Windows** na barra de pesquisa e, em seguida, selecione o nome de serviço.

      > [!div class="mx-imgBorder"]
      > ![Uma imagem de um utilizador que seleciona "Windows Virtual Desktop" a partir do menu suspenso da barra de pesquisa no portal Azure. "Windows Virtual Desktop" é destacado a vermelho.](media/find-and-select.png)

3. Selecione a piscina de anfitriões onde planeia colocar as aplicações MSIX.

4. Selecione **pacotes MSIX** para abrir a grelha de dados com todos os **pacotes MSIX adicionados** ao pool anfitrião.

5. **Selecione + Adicione** para abrir o **separador pacote Add MSIX.**

6. No **separador pacote Add MSIX,** introduza os seguintes valores:

      - Para **o caminho da imagem MSIX,** insira um caminho válido do UNC apontando para a imagem MSIX na partilha de ficheiros. (Por exemplo, `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` .) Quando terminar, **selecione Adicione** para interrogar o recipiente MSIX para verificar se o caminho é válido.

      - Para **o pacote MSIX,** selecione o nome do pacote MSIX relevante no menu suspenso. Este menu só será preenchido se tiver introduzido um caminho de imagem válido no **caminho de imagem MSIX**.

      - Para **aplicações pacotes**, certifique-se de que a lista contém todas as aplicações MSIX que deseja estar disponível para os utilizadores no seu pacote MSIX.

      - Opcionalmente, **insira** um nome de Exibição se quiser que o seu pacote tenha um pacote mais fácil de utilizar nas suas implementações do utilizador.

      - Certifique-se de que a **versão** tem o número de versão correto.

      - Selecione o **tipo de Registo** que pretende utilizar. Qual deles usas depende das tuas necessidades:

    - **O registo a pedido** adia o registo integral da aplicação MSIX até que o utilizador inicie a aplicação. Este é o tipo de registo que recomendamos que utilize.

    - **Faça login apenas** nos registos enquanto o utilizador está a iniciar sessão. Não recomendamos este tipo porque pode levar a tempos de início de s inscrição mais longos para os utilizadores.

7.  Para **Estado,** selecione o seu estado preferido.
    -  O estado **Ative** permite que os utilizadores interajam com o pacote.
    -  O estado **inativo** faz com que o Windows Virtual Desktop ignore o pacote e não o entregue aos utilizadores.

8. Quando terminar, **selecione Add**.

## <a name="publish-msix-apps-to-an-app-group"></a>Publicar aplicativos MSIX para um grupo de aplicações

Em seguida, terá de publicar as aplicações no pacote. Terá de o fazer tanto para grupos de aplicações de ambiente de trabalho como para grupos de aplicações remotas.

Se já tem uma imagem MSIX, avance para [publicar as aplicações MSIX para um grupo de aplicações.](#publish-msix-apps-to-an-app-group) Se pretender testar aplicações antigas, siga as instruções na [Criar um pacote MSIX a partir de um instalador de ambiente de trabalho num VM](/windows/msix/packaging-tool/create-app-package-msi-vm/) para converter a aplicação legacy num pacote MSIX.

Para publicar as aplicações:

1. No fornecedor de recursos virtual desktop do Windows, selecione o **separador Grupos de Aplicação.**

2. Selecione o grupo de aplicações para o quais pretende publicar as aplicações.

   >[!NOTE]
   >As aplicações MSIX podem ser entregues com app MSIX anexada tanto a grupos de aplicações remotas como a grupos de aplicações de desktop

3. Uma vez que esteja no grupo de aplicações, selecione o **separador Aplicações.** A grelha **de Aplicações** apresentará todas as aplicações existentes dentro do grupo de aplicações.

4. **Selecione + Adicione** para abrir o **separador de aplicação Adicionar.**

      > [!div class="mx-imgBorder"]
      > ![Uma imagem de imagem do utilizador selecionando + Adicionar para abrir o separador de aplicação adicionar](media/select-add.png)

5. Para **fonte de aplicação,** escolha a fonte para a sua aplicação.
    - Se estiver a utilizar um grupo de aplicações desktop, escolha o **pacote MSIX**.
      
      > [!div class="mx-imgBorder"]
      > ![Uma imagem de um cliente que seleciona o pacote MSIX do menu de entrega de fonte de aplicação. O pacote MSIX é destacado a vermelho.](media/select-source.png)
    
    - Se estiver a utilizar um grupo de aplicações remotos, escolha uma das seguintes opções:
        
        - Menu Iniciar
        - Caminho da aplicação
        - Pacote MSIX

    - Para **nome de aplicação,** introduza um nome descritivo para a aplicação.

    Também pode configurar as seguintes características opcionais:
   
    - Para **o nome Display**, introduza um novo nome para o pacote que os seus utilizadores irão ver.

    - Para **descrição,** introduza uma breve descrição do pacote de aplicações.

    - Se estiver a utilizar um grupo de aplicações remotos, também pode configurar estas opções:

        - **Caminho do ícone**
        - **Índice de ícone**
        - **Mostrar no feed web**

6. Quando tiver terminado, selecione **Guardar**.

>[!NOTE]
>Quando um utilizador é designado para o grupo de aplicações remotas e grupo de aplicações de desktop a partir do mesmo pool anfitrião, o grupo de aplicações de ambiente de trabalho será apresentado no feed.

## <a name="assign-a-user-to-an-app-group"></a>Atribuir um utilizador a um grupo de aplicações

Depois de atribuir aplicações MSIX a um grupo de aplicações, terá de conceder aos utilizadores acesso às suas aplicações. Pode atribuir acesso adicionando utilizadores ou grupos de utilizadores a um grupo de aplicações com aplicações MSIX publicadas. Siga as instruções em [Gerir grupos de aplicações com o portal Azure](manage-app-groups.md) para atribuir os seus utilizadores a um grupo de aplicações.

>[!NOTE]
>A aplicação MSIX anexar aplicações remotas pode desaparecer do feed quando testa aplicações remotas durante a pré-visualização pública. As aplicações não aparecem porque o pool de anfitriões que você está usando no ambiente de avaliação está sendo servido por um Corretor RD no ambiente de produção. Como o Corretor RD no ambiente de produção não regista a presença da app MSIX anexar aplicações remotas, as aplicações não aparecerão no feed.

## <a name="change-msix-package-state"></a>Alterar estado de pacote MSIX

Em seguida, terá de alterar o estado do pacote MSIX para **Ative** ou **Inative,** dependendo do que pretende fazer com o pacote. Os pacotes ativos são pacotes com os qual os seus utilizadores podem interagir assim que forem publicados. Os pacotes inativos são ignorados pelo Windows Virtual Desktop, pelo que os seus utilizadores não podem interagir com as aplicações no seu interior.

### <a name="change-state-with-the-applications-list"></a>Alterar estado com a lista de Candidaturas

Para alterar o estado do pacote com a lista de Aplicações:

1. Vá à sua piscina de anfitriões e selecione **pacotes MSIX**. Você deve ver uma lista de todos os pacotes MSIX existentes dentro da piscina de anfitrião.

2. Selecione os pacotes MSIX cujos estados precisa de alterar e, em seguida, selecione **Alterar estado**.

### <a name="change-state-with-update-package"></a>Alterar estado com pacote de atualização

Para alterar o estado do pacote com um pacote de atualização:

1. Vá à sua piscina de anfitriões e selecione **pacotes MSIX**. Você deve ver uma lista de todos os pacotes MSIX existentes dentro da piscina de anfitrião.

2. Selecione o nome do pacote cujo estado pretende alterar da lista de pacotes MSIX. Isto abrirá o **separador pacote 'Actualização'.**

3. Alternar o interruptor **do Estado** para **Inativo** ou **Ativo** e, em seguida, selecione **Save.**

## <a name="change-msix-package-registration-type"></a>Alterar o tipo de registo de pacote MSIX

Para alterar o tipo de registo do pacote:

1. Selecione **pacotes MSIX**. Você deve ver uma lista de todos os pacotes MSIX existentes dentro da piscina de anfitrião.

2. Selecione **o nome do pacote na** grelha de **pacotes MSIX** isto abrirá a lâmina para atualizar a embalagem.

3. Alternar o **tipo de Registo** através do botão de bloqueio **On-demand/Log,** conforme pretendido e selecione **Guardar.**

## <a name="remove-an-msix-package"></a>Remover um pacote MSIX

Para remover um pacote MSIX da sua piscina de anfitrião:

1. Selecione **pacotes MSIX**.  Você deve ver uma lista de todos os pacotes MSIX existentes dentro da piscina de anfitrião.

2. Selecione a elipse no lado direito o nome da embalagem que pretende eliminar e, em seguida, selecione **Remover**.

## <a name="remove-msix-apps"></a>Remover aplicativos MSIX

Para remover aplicações MSIX individuais do seu pacote:

1. Vá à piscina anfitriã e selecione **Grupos de Aplicação.**

2. Selecione o grupo de aplicações de que pretende remover as aplicações MSIX.

3. Abra o **separador Aplicações.**

4. Selecione a aplicação que pretende remover e, em seguida, selecione **Remover**.

## <a name="next-steps"></a>Passos seguintes

Faça perguntas à nossa comunidade sobre esta funcionalidade no [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Também pode deixar feedback para o Windows Virtual Desktop no [centro de feedback virtual do Windows Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Aqui estão outros artigos que pode achar útil:

- [App MSIX anexa glossário](app-attach-glossary.md)
- [App MSIX anexa FAQ](app-attach-faq.md)
