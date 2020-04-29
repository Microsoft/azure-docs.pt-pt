---
title: Criar um laboratório para ensinar desenvolvimento de aplicações móveis com o Android Studio
titleSuffix: Azure Lab Services
description: Aprenda a criar um laboratório para ensinar a classe de desenvolvimento de aplicações móveis de dados que utiliza o Android Studio.  O artigo também discutirá ajustes a fazer ao utilizar o Android Studio numa máquina virtual em Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76849789"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Criar um laboratório para ensinar o desenvolvimento de aplicações móveis de dados com o Android Studio

Este artigo irá mostrar-lhe como criar uma classe de desenvolvimento de aplicações móveis introdutórias.  Esta classe foca-se em aplicações móveis Android que podem ser publicadas na [Google Play Store.](https://play.google.com/store/apps)  Os alunos aprendem a usar o [Android Studio](https://developer.android.com/studio) para construir aplicações.  [O Emulador de Estúdio Visual para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) é usado para testar a aplicação localmente.

## <a name="lab-configuration"></a>Configuração do laboratório

Para montar este laboratório, precisa de uma subscrição azure e uma conta de laboratório para começar. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que conseguir uma subscrição do Azure, pode criar uma nova conta de laboratório nos Serviços de Laboratório Azure. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte o tutorial para criar uma conta de [laboratório.](tutorial-setup-lab-account.md)  Também pode usar uma conta de laboratório existente.

Siga o tutorial de [laboratório de sala de aula para](tutorial-setup-classroom-lab.md) criar um novo laboratório e, em seguida, aplique as seguintes definições:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- |
| Médio (Virtualização Aninhada) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Configuração da máquina de modelo

Quando a criação da máquina de modelo estiver completa, [ligue a máquina e ligue-a](how-to-create-manage-template.md#update-a-template-vm) para completar as seguintes tarefas:

1. Adicionar papel hyper-V
2. Descarregue e instale Java.  
3. Descarregue e instale o Emulador de Estúdio Visual para Android.
4. Descarregue e instale o Android Studio.
5. Configure Visual Studio Emulator para Android Studio.

## <a name="add-hyper-v-role"></a>Adicionar papel hyper-V

O Hyper-V deve ser ativado para a instalação bem sucedida de Emulator visual studio para Android.  Siga as instruções sobre [como ativar a virtualização aninhada num](how-to-enable-nested-virtualization-template-vm.md) artigo de máquina virtual modelo.

## <a name="install-java"></a>Instalar Java

Android Studio requer Java.  Siga os passos abaixo para descarregar a versão mais recente de Java.

1. Navegue para a página de download de [Java.](https://www.java.com/download/) Clique no botão **Java Download.**
2. No Windows de 64 bits para a página web de Java, clique no botão com o rótulo **"Concordar e Começar Livre Download**".
3. Quando aparecer o instalador **java Setup,** clique em **Instalar**.
4. Aguarde até que o título do instalador mude para **a Configuração Java – Complete**.  Clique no botão **Fechar.**

## <a name="install-visual-studio-emulator-for-android"></a>Instale o Emulador de Estúdio Visual para Android

Para testar uma aplicação Android localmente, deve utilizar uma versão virtualizada de um dispositivo Android.  Existem alguns emuladores Android disponíveis que permitirão a um desenvolvedor testar a sua aplicação a partir da sua máquina.  Estamos a usar o Emulator visual studio para Android porque é um emulador que suporta a virtualização aninhada.  Como o VM do Serviço de Laboratório já é uma máquina virtual, precisamos de um emulador que suporte a virtualização aninhada.  O emulador incorporado para o Android Studio não suporta a virtualização aninhada.  Para ver quais os emuladores que suportam a virtualização aninhada, consulte [a aceleração do hardware para o desempenho do emulador (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Utilize as seguintes instruções para descarregar e instalar o Emulator do Estúdio Visual para Android.

1. Navegue para [Emulator de Estúdio Visual para](https://visualstudio.microsoft.com/vs/msft-android-emulator/) página inicial do Android.
2. Clique no botão Descarregue o botão **Emulator.**
3. Quando vs_emulatorsetup.exe for descarregado, execute o executável.
4. Quando o diálogo de configuração do Estúdio Visual aparecer, clique no botão **Instalar.**
5. Aguarde que o instalador esteja completo.  Clique no botão **Restart Now** para reiniciar o computador e terminar a instalação.

Inicie primeiro o emulador antes de implementar a sua aplicação utilizando o Android Studio.  Para obter mais informações sobre o Emulator do Estúdio Visual para Android, consulte [Visual Studio Emulator para documentação Android](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Instalar O Estúdio Android

Siga as instruções abaixo para descarregar e instalar [o Android Studio](https://developer.android.com/studio).

1. Navegue para [a página de descarregamento do Android Studio.](https://developer.android.com/studio#downloads)  
    > [!NOTE]
    > O Internet Explorer não é suportado por este site.
2. Clique no pacote Android Studio executável do Windows (64 bits).
3. Leia os termos legais escritos no pop-up.  Quando estiver pronto para continuar, verifique **se li e concordo com os termos e condições acima** referidos e clique no Download Android Studio para o botão **Windows.**
4. Uma vez que a configuração do Android Studio executada é descarregada, execute o executável.
5. Na página **welcome to Android Studio Setup** do instalador Android Studio **Setup,** clique em **Next**.
6. Na página Definições de **Configuração,** clique em **Seguinte**.
7. Na página **'Escolher 'Menu Iniciar',** clique em **Instalar**.
8. Aguarde a configuração completa.
9. Na página **'Instalação Completa',** clique em **Seguinte**.
10. Na página de Configuração do **Estúdio Android Completeing.**  Clique em **Concluir**.
11. O Android Studio começará automaticamente depois de a configuração estar concluída.
12. Nas **definições do Android importação A partir de...** dialona, selecione **Não importe configurações**. Clique em **OK**.
13. Na página **de boas-vindas** do Assistente de Configuração do **Estúdio Android,** clique em **Next**.
14. Na página **Tipo de Instalação,** escolha **Standard**. Clique em **Seguinte**.
15. Na página **tema Select UI,** selecione tema pretendido. Clique em **Seguinte**.
16. Na página **'Verificar Definições',** clique em **Seguinte**.
17. Na página **de Componentes de Descarregamento,** aguarde até que todos os componentes sejam descarregados.  Clique em **Concluir**.

    > [!IMPORTANT]
    > Espera-se que a instalação HAXM falhe.  HaXM não suporta a virtualização aninhada, razão pela qual instalámos o Emulador visual studio para Android no início deste artigo.

18. O diálogo **Welcome to Android Studio** aparecerá quando o assistente de configuração estiver completo.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Configure Android Studio e Emulator de Estúdio Visual para Android

O Android Studio está quase pronto para ser utilizado.  Ainda precisamos de dizer ao Emulator visual Studio para Android onde o Android SDK está instalado.  Isto fará com que quaisquer emuladores em execução no Visual Studio para Android mostram como alvos de implementação para a depuração do Android Studio.

Precisamos definir uma chave de registo específica para dizer visual studio emulator para Android onde o Android Sdk está localizado.  Para definir a chave de registo necessária, execute o script abaixo.  O script PowerShell abaixo assume a localização de instalação padrão para o Android Sdk.  Se instalou o seu Android Sdk noutro `$androidSdkPath` local, modifique o valor para antes de executar o script.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Restart Visual Studio Emulator para Android e Android Studio para que a nova configuração seja usada.

Inicie a versão de que precisa no Emulator do Estúdio Visual.  Ele aparecerá como um alvo de implementação para a sua aplicação Android em estúdio Android.  A versão mínima para o projeto Android Studio deve suportar a versão em execução no Emulator visual studio para Android.  Agora está pronto para criar e depurar projetos usando Android Studio e Visual Studio Emulator para Android.  Se tiver algum problema, consulte o emulador Android.

## <a name="cost"></a>Custo

Se quiser estimar o custo deste laboratório, pode seguir o exemplo abaixo.
Para uma turma de 25 alunos com 20 horas de horário de aulas programada e 10 horas de quota para trabalhos de casa ou tarefas, o preço para o laboratório seria  

25 \* alunos (20 programados + 10 quotas) horas * 55 Unidades de Laboratório * 0,01 USD por hora = 412,5 USD

Mais detalhes sobre os preços, consulte o [Preço dos Serviços do Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns à criação de qualquer laboratório.

- [Criar e gerir um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)
