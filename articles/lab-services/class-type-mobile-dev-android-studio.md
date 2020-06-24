---
title: Crie um laboratório para ensinar desenvolvimento de aplicações móveis com o Android Studio
titleSuffix: Azure Lab Services
description: Saiba como criar um laboratório para ensinar a aula de desenvolvimento de aplicações móveis de dados que usa o Android Studio.  O artigo também discutirá os ajustes a fazer ao usar o Android Studio numa máquina virtual em Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84899033"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Crie um laboratório para ensinar desenvolvimento de aplicações móveis de dados com o Android Studio

Este artigo irá mostrar-lhe como configurar uma aula de desenvolvimento de aplicações móveis introdutórias.  Esta classe foca-se em aplicações móveis Android que podem ser publicadas na [Google Play Store.](https://play.google.com/store/apps)  Os alunos aprendem a usar o [Android Studio](https://developer.android.com/studio) para construir aplicações.  [O Emulador visual do Estúdio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) é utilizado para testar a aplicação localmente.

## <a name="lab-configuration"></a>Configuração de laboratório

Para montar este laboratório, precisa de uma assinatura Azure e uma conta de laboratório para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que tiver uma assinatura Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [tutorial para criar uma conta de laboratório.](tutorial-setup-lab-account.md)  Também pode usar uma conta de laboratório existente.

Siga o tutorial de [laboratório de sala de aula configurado](tutorial-setup-classroom-lab.md) para criar um novo laboratório e, em seguida, aplique as seguintes definições:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- |
| Médio (Virtualização Aninhada) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Configuração da máquina do modelo

Quando a criação da máquina do modelo estiver concluída, [inicie a máquina e conecte-a](how-to-create-manage-template.md#update-a-template-vm) para completar as seguintes tarefas:

1. Adicionar função Hiper-V
2. Descarregue e instale Java.  
3. Descarregue e instale o Emulator Visual Studio para Android.
4. Faça o download e instale o Android Studio.
5. Configure o Emulador visual do Estúdio Visual para o Android Studio.

## <a name="add-hyper-v-role"></a>Adicionar função Hiper-V

O Hiper-V deve ser ativado para a instalação bem sucedida do Emulador visual do Estúdio para Android.  Siga as instruções de [como permitir a virtualização aninhada num](how-to-enable-nested-virtualization-template-vm.md) artigo de máquina virtual de modelo.

## <a name="install-java"></a>Instalar Java

O Android Studio requer Java.  Siga os passos abaixo para baixar a versão mais recente de Java.

1. Navegue para a [página de descarregamento de Java](https://www.java.com/download/). Clique no botão **Java Descarregar.**
2. Na página web do Windows de 64 bits para Java, clique no botão marcado **"Concordar" e iniciar**o download gratuito.
3. Quando aparecer o instalador **de configuração Java,** clique em **Instalar**.
4. Aguarde até que o título do instalador mude para **a Configuração de Java – Complete**.  Clique no botão **Fechar.**

## <a name="install-visual-studio-emulator-for-android"></a>Instalar o Emulador visual do Estúdio para Android

Para testar uma aplicação Android localmente, deve utilizar uma versão virtualizada de um dispositivo Android.  Existem alguns emuladores Android disponíveis que permitirão a um desenvolvedor testar a sua aplicação a partir da sua máquina.  Estamos a usar o Visual Studio Emulator para Android porque é um emulador que suporta a virtualização aninhada.  Como o Lab Service VM já é uma máquina virtual, precisamos de um emulador que suporte a virtualização aninhada.  O emulador incorporado para Android Studio não suporta a virtualização aninhada.  Para ver quais os emuladores que suportam a virtualização aninhada, consulte [a aceleração do hardware para o desempenho do emulador (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Utilize as seguintes instruções para descarregar e instalar o Emulador Visual Studio para Android.

1. Navegue para [o Emulator visual studio para a](https://visualstudio.microsoft.com/vs/msft-android-emulator/) página inicial do Android.
2. Clique no botão Descarregar o botão **Emulator.**
3. Quando vs_emulatorsetup.exe for descarregado, execute o executável.
4. Quando o diálogo de configuração do Estúdio Visual aparecer, clique no botão **Instalar.**
5. Aguarde que o instalador esteja completo.  Clique no botão **Reiniciar Agora** para reiniciar o computador e terminar a instalação.

Inicie primeiro o emulador antes de implementar a sua aplicação utilizando o Android Studio.  Para obter mais informações sobre o Emulador visual do Estúdio para Android, consulte [o Emulador visual do Estúdio para documentação Android.](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)

## <a name="install-android-studio"></a>Instalar o Estúdio Android

Siga as instruções abaixo para descarregar e instalar [o Android Studio](https://developer.android.com/studio).

1. Navegue para a [página de descarregamento do Android Studio](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > O Internet Explorer não é suportado por este site.
2. Clique no pacote Android Studio executável do Windows (64 bits).
3. Leia os termos legais escritos no pop-up.  Quando estiver pronto para continuar, verifique **se li e concordo com os termos e condições acima e** clique no Download Android Studio para o botão **Windows.**
4. Uma vez que a configuração do Android Studio é executável é descarregada, execute o executável.
5. Na página **welcome to Android Studio Setup** do instalador Android Studio **Setup,** clique em **Seguinte**.
6. Na página Definições de **Configuração,** clique em **Seguinte**.
7. Na página **'Escolha Página de Pasta do Menu Iniciar',** clique em **Instalar**.
8. Aguarde que a instalação esteja completa.
9. Na página **'Concluir' de Instalação,** clique em **Seguinte**.
10. Na página **completa da configuração do estúdio Android.**  Clique em **Concluir**.
11. O Android Studio começará automaticamente depois de concluída a configuração.
12. Nas **definições** do Android de Importação a partir de... diálogo, selecione **Não importe as definições**. Clique em **OK**.
13. Na página de **boas-vindas** do **Android Studio Setup Wizard**, clique em **Seguinte**.
14. Na página **'Tipo de Instalação',** escolha **Standard**. Clique em **Seguinte**.
15. Na página **Tema Select UI,** selecione o tema pretendido. Clique em **Seguinte**.
16. Na página **'Verificar Definições',** clique em **Seguinte**.
17. Na página **'Descarregar componentes',** aguarde até que todos os componentes sejam descarregados.  Clique em **Concluir**.

    > [!IMPORTANT]
    > Espera-se que a instalação HAXM falhe.  O HAXM não suporta a virtualização aninhada, razão pela qual instalámos o Visual Studio Emulator para Android no início deste artigo.

18. O **diálogo Welcome to Android Studio** aparecerá quando o assistente de configuração estiver completo.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Configurar o Android Studio e o Visual Studio Emulator para Android

O Android Studio está quase pronto para ser utilizado.  Ainda precisamos de dizer ao Visual Studio Emulator para Android onde está instalado o Android SDK.  Isto fará com que quaisquer emuladores em execução no Visual Studio para Android mostrem como alvos de implementação para o Android Studio depuração.

Precisamos definir uma chave de registo específica para dizer ao Visual Studio Emulator para Android onde está o Android Sdk.  Para definir a chave de registo necessária, execute o script abaixo.  O script PowerShell abaixo pressupõe a localização de instalação padrão para o Android Sdk.  Se instalou o seu Android Sdk noutro local, modifique o valor antes `$androidSdkPath` de executar o script.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Reinicie o Emulador visual do Estúdio para Android e Android Studio para que a nova definição seja usada.

Inicie a versão que precisa no Emulador visual do Estúdio.  Será um alvo de implementação para a sua aplicação Android no estúdio Android.  A versão mínima para o projeto Android Studio deve suportar a versão em execução no Emulator Visual Studio para Android.  Agora está pronto para criar e depurar projetos usando o Android Studio e o Visual Studio Emulator para Android.  Se tiver algum problema, consulte o emulador Android para resolver problemas.

## <a name="cost"></a>Custo

Se quiser estimar o custo deste laboratório, pode seguir o exemplo abaixo.
Para uma turma de 25 alunos com 20 horas de horário de aula programado e 10 horas de quota para trabalhos de casa ou tarefas, o preço para o laboratório seria  

25 alunos \* (20 horários + 10 quota) horas * 55 Unidades de Laboratório * 0,01 USD por hora = 412,5 USD

Mais detalhes sobre os preços, consulte o [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns para montar qualquer laboratório.

- [Criar e gerir um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)
