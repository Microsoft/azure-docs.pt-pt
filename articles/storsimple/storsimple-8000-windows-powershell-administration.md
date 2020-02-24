---
title: Gerenciamento de dispositivo do PowerShell para StorSimple
description: Saiba como usar Windows PowerShell para StorSimple para gerenciar seu dispositivo StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 7d59f00d655bc7b2395c46713a56f52c61ffa42c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277096"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Usar Windows PowerShell para StorSimple para administrar seu dispositivo

## <a name="overview"></a>Visão geral

O Windows PowerShell para StorSimple fornece uma interface de linha de comando que você pode usar para gerenciar seu dispositivo de Microsoft Azure StorSimple. Como o nome sugere, é uma interface de linha de comando baseada no Windows PowerShell que é criada em um runspace restrito. Da perspectiva do usuário na linha de comando, um runspace restrito aparece como uma versão restrita do Windows PowerShell. Ao mesmo tempo em que mantém alguns dos recursos básicos do Windows PowerShell, essa interface tem cmdlets dedicados adicionais que são voltados para gerenciar seu dispositivo de Microsoft Azure StorSimple.

Este artigo descreve os recursos de Windows PowerShell para StorSimple, incluindo como você pode se conectar a essa interface e contém links para procedimentos passo a passo ou fluxos de trabalho que você pode executar usando essa interface. Os fluxos de trabalho incluem como registrar seu dispositivo, configurar a interface de rede em seu dispositivo, instalar atualizações que exigem que o dispositivo esteja no modo de manutenção, alterar o estado do dispositivo e solucionar quaisquer problemas que você possa ter.

Depois de ler este artigo, você poderá:

* Conecte-se ao seu dispositivo StorSimple usando Windows PowerShell para StorSimple.
* Administre seu dispositivo StorSimple usando o Windows PowerShell para StorSimple.
* Obtenha ajuda no Windows PowerShell para StorSimple.

> [!NOTE]
> * Windows PowerShell para StorSimple cmdlets permitem que você gerencie seu dispositivo StorSimple de um console serial ou remotamente por meio da comunicação remota do Windows PowerShell. Para obter mais informações sobre cada um dos cmdlets individuais que podem ser usados nesta interface, acesse [referência de cmdlet para Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Os cmdlets Azure PowerShell StorSimple são uma coleção diferente de cmdlets que permitem automatizar tarefas de nível de serviço e migração do StorSimple na linha de comando. Para obter mais informações sobre os cmdlets Azure PowerShell para StorSimple, vá para a [referência de cmdlet do Azure StorSimple](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


Você pode acessar o Windows PowerShell para StorSimple usando um dos seguintes métodos:

* [Conectar-se ao console serial do dispositivo StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Conectar-se remotamente ao StorSimple usando o Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Conectar-se a Windows PowerShell para StorSimple por meio do console serial do dispositivo

Você pode [baixar](https://www.putty.org/) o software de simulação de terminal de saída ou semelhante para se conectar ao Windows PowerShell para StorSimple. Você precisa configurar a reproduzida especificamente para acessar o dispositivo Microsoft Azure StorSimple. Os tópicos a seguir contêm etapas detalhadas sobre como configurar a retenção e conectar-se ao dispositivo. Várias opções de menu no console serial também são explicadas.

### <a name="putty-settings"></a>Definições do PuTTY

Certifique-se de usar as seguintes configurações de reutilização para se conectar à interface do Windows PowerShell por meio do console serial.

#### <a name="to-configure-putty"></a>Para configurar o acabamento

1. Na caixa de diálogo **reconfiguração de redefinição** , no painel **categoria** , selecione **teclado**.
2. Verifique se as opções a seguir estão selecionadas (essas são as configurações padrão quando você inicia uma nova sessão).
   
   | Item de teclado | Selecione |
   | --- | --- |
   | Tecla backspace |Controle-? (127) |
   | Chaves inicial e final |Padrão |
   | Teclas de função e teclado |ESC [n ~ |
   | Estado inicial das teclas de cursor |Normal |
   | Estado inicial do teclado numérico |Normal |
   | Habilitar recursos adicionais do teclado |Control-Alt é diferente de AltGr |
   
    ![Configurações de saída com suporte](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Clique em **Aplicar**.
4. No painel **categoria** , selecione **tradução**.
5. Na caixa de listagem **conjunto de caracteres remoto** , selecione **UTF-8**.
6. Em **manipulação de caracteres de desenho de linha**, selecione **usar pontos de código de desenho de linha Unicode**. A captura de tela a seguir mostra as seleções de recorreções corretas.
   
    ![Configurações de saída UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Clique em **Aplicar**.

Agora você pode usar a saída para se conectar ao console serial do dispositivo executando as etapas a seguir.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Sobre o console serial

Quando você acessa a interface do Windows PowerShell de seu dispositivo StorSimple por meio do console serial, uma mensagem de banner é apresentada, seguida por opções de menu.

A mensagem de faixa contém informações básicas do dispositivo StorSimple, como o modelo, o nome, a versão do software instalado e o status do controlador que você está acessando. A imagem a seguir mostra um exemplo de uma mensagem de cabeçalho.

![Mensagem de faixa serial](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Você pode usar a mensagem de faixa para identificar se o controlador ao qual você está conectado está _ativo_ ou _passivo_.

A imagem a seguir mostra as várias opções de runspace disponíveis no menu do console serial.

![Registrar seu dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Você pode escolher entre as seguintes configurações:

1. **Fazer logon com acesso completo** Essa opção permite que você se conecte (com as credenciais apropriadas) ao runspace **SSAdminConsole** no controlador local. (O controlador local é o controlador que você está acessando no momento por meio do console serial do seu dispositivo StorSimple.) Essa opção também pode ser usada para permitir que Suporte da Microsoft acessem o runspace irrestrito (uma sessão de suporte) para solucionar possíveis problemas de dispositivo. Depois de usar a opção 1 para fazer logon, você pode permitir que o engenheiro de Suporte da Microsoft acesse o runspace irrestrito executando um cmdlet específico. Para obter detalhes, consulte [iniciar uma sessão de suporte](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Fazer logon no controlador de par com acesso completo** Essa opção é igual à opção 1, exceto que você pode se conectar (com as credenciais apropriadas) ao runspace **SSAdminConsole** no controlador de par. Como o dispositivo StorSimple é um dispositivo de alta disponibilidade com dois controladores em uma configuração ativa-passiva, o par refere-se ao outro controlador no dispositivo que você está acessando por meio do console serial.
   Semelhante à opção 1, essa opção também pode ser usada para permitir que Suporte da Microsoft acessem o runspace irrestrito em um controlador de par.

3. **Conectar-se com acesso limitado** Essa opção é usada para acessar a interface do Windows PowerShell em modo limitado. Suas credenciais de acesso não são solicitadas. Essa opção se conecta a um runspace mais restrito em comparação com as opções 1 e 2.  Algumas das tarefas que estão disponíveis por meio da opção 1 que **não podem* ser executadas nesse runspace são:
   
   * Redefinir para as configurações de fábrica
   * Alterar a palavra-passe
   * Habilitar ou desabilitar o acesso de suporte
   * Aplicar atualizações
   * Instalar hotfixes

     > [!NOTE]
     > Essa é a opção preferida se você esqueceu a senha de administrador do dispositivo e não pode se conectar por meio da opção 1 ou 2.

4. **Alterar idioma** Essa opção permite que você altere o idioma de exibição na interface do Windows PowerShell. Os idiomas com suporte são Inglês, japonês, russo, francês, sul coreano, espanhol, italiano, alemão, chinês e português (Brasil).

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Conectar-se remotamente ao StorSimple usando o Windows PowerShell para StorSimple

Você pode usar a comunicação remota do Windows PowerShell para se conectar ao seu dispositivo StorSimple. Ao se conectar dessa forma, você não verá um menu. (Você verá um menu somente se usar o console serial no dispositivo para se conectar. Conectar-se remotamente leva você diretamente ao equivalente de "opção 1 – acesso completo" no console serial.) Com a comunicação remota do Windows PowerShell, você se conecta a um runspace específico. Você também pode especificar o idioma de exibição.

O idioma de exibição é independente do idioma que você definiu usando a opção **Alterar idioma** no menu do console serial. O PowerShell remoto selecionará automaticamente a localidade do dispositivo do qual você está se conectando, se nenhuma for especificada.

> [!NOTE]
> Se você estiver trabalhando com hosts virtuais Microsoft Azure e dispositivos de nuvem StorSimple, poderá usar a comunicação remota do Windows PowerShell e o host virtual para se conectar ao dispositivo de nuvem. Se você tiver configurado um local de compartilhamento no host no qual salvar informações da sessão do Windows PowerShell, deverá estar ciente de _que a entidade_ de segurança inclui apenas usuários autenticados. Portanto, se você tiver configurado o compartilhamento para permitir o acesso por _todos_ e se conectar sem especificar credenciais, a entidade anônima não autenticada será usada e você verá um erro. Para corrigir esse problema, no host de compartilhamento, você deve habilitar a conta de convidado e, em seguida, dar à conta de convidado acesso completo ao compartilhamento ou deve especificar credenciais válidas junto com o cmdlet do Windows PowerShell.


Você pode usar HTTP ou HTTPS para se conectar via comunicação remota do Windows PowerShell. Use as instruções nos seguintes tutoriais:

* [Conectar-se remotamente usando HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Conectar-se remotamente usando HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Considerações de segurança de conexão

Quando você estiver decidindo como se conectar ao Windows PowerShell para StorSimple, considere o seguinte:

* Conectar-se diretamente ao console serial do dispositivo é seguro, mas conectar-se ao console serial por meio de comutadores de rede não é. Tenha cuidado com o risco de segurança ao se conectar à serial do dispositivo em comutadores de rede.
* A conexão por meio de uma sessão HTTP pode oferecer mais segurança do que a conexão por meio do console serial pela rede. Embora esse não seja o método mais seguro, ele é aceitável em redes confiáveis.
* A conexão por meio de uma sessão HTTPS é a opção mais segura e recomendada.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrar seu dispositivo StorSimple usando o Windows PowerShell para StorSimple

A tabela a seguir mostra um resumo de todas as tarefas comuns de gerenciamento e fluxos de trabalho complexos que podem ser executados na interface do Windows PowerShell de seu dispositivo StorSimple. Para obter mais informações sobre cada fluxo de trabalho, clique na entrada apropriada na tabela.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Fluxos de trabalho Windows PowerShell para StorSimple

| Se você quiser fazer isso... | Use este procedimento. |
| --- | --- |
| Registrar seu dispositivo |[Configurar e registrar o dispositivo usando Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Configurar proxy Web</br>Exibir configurações de proxy Web |[Configurar o proxy Web para seu dispositivo StorSimple](storsimple-8000-configure-web-proxy.md) |
| Modificar as configurações de interface de rede de dados 0 em seu dispositivo |[Modificar a interface de rede DATA 0 para seu dispositivo StorSimple](storsimple-8000-modify-data-0.md) |
| Parar um controlador </br> Reiniciar ou desligar um controlador </br> Desligar um dispositivo</br>Redefinir o dispositivo para as configurações padrão de fábrica |[Gerenciar controladores de dispositivo](storsimple-8000-manage-device-controller.md) |
| Instalar atualizações e hotfixes do modo de manutenção |[Atualizar o seu dispositivo](storsimple-update-device.md) |
| Entrar no modo de manutenção </br>Sair do modo de manutenção |[Modos de dispositivo StorSimple](storsimple-8000-device-modes.md) |
| Criar um pacote de suporte</br>Descriptografar e editar um pacote de suporte |[Criar e gerenciar um pacote de suporte](storsimple-8000-create-manage-support-package.md) |
| Iniciar uma sessão de suporte</br> |[Iniciar uma sessão de suporte no Windows PowerShell para StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Obter ajuda no Windows PowerShell para StorSimple

No Windows PowerShell para StorSimple, a ajuda do cmdlet está disponível. Uma versão online atualizada dessa ajuda também está disponível, que pode ser usada para atualizar a ajuda em seu sistema.

Obter ajuda nessa interface é semelhante ao do Windows PowerShell, e a maioria dos cmdlets relacionados à ajuda funcionará. Você pode encontrar ajuda para o Windows PowerShell online: [Microsoft. PowerShell. Core](/powershell/module/Microsoft.PowerShell.Core/).

Veja a seguir uma breve descrição dos tipos de ajuda para esta interface do Windows PowerShell, incluindo como atualizar a ajuda.

### <a name="to-get-help-for-a-cmdlet"></a>Para obter ajuda para um cmdlet

* Para obter ajuda para qualquer cmdlet ou função, use o seguinte comando: `Get-Help <cmdlet-name>`
* Para obter ajuda online para qualquer cmdlet, use o cmdlet anterior com o parâmetro `-Online`: `Get-Help <cmdlet-name> -Online`
* Para obter ajuda completa, você pode usar o parâmetro `–Full` e, para obter exemplos, use o parâmetro `–Examples`.

### <a name="to-update-help"></a>Para atualizar a ajuda

Você pode atualizar facilmente a ajuda na interface do Windows PowerShell. Execute as etapas a seguir para atualizar a ajuda em seu sistema.

#### <a name="to-update-cmdlet-help"></a>Para atualizar a ajuda do cmdlet
1. Inicie o Windows PowerShell com a opção **Executar como administrador** .
2. No prompt de comando, digite: `Update-Help`
3. Os arquivos de ajuda atualizados serão instalados.
4. Depois que os arquivos de ajuda forem instalados, digite: `Get-Help Get-Command`. Isso exibirá uma lista de cmdlets para os quais a ajuda está disponível.

> [!NOTE]
> Para obter uma lista de todos os cmdlets disponíveis em um runspace, faça logon na opção de menu correspondente e execute o cmdlet `Get-Command`.


## <a name="next-steps"></a>Passos seguintes

Se você tiver problemas com o dispositivo StorSimple ao executar um dos fluxos de trabalho acima, consulte [ferramentas para solucionar problemas de implantações do storsimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

