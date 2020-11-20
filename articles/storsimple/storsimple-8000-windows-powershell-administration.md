---
title: PowerShell para gestão de dispositivos StorSimple
description: Saiba como utilizar o Windows PowerShell para storSimple para gerir o seu dispositivo StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 65e9657c3948d8ce5883cd33ca8720f501352105
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950674"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Utilizar o Windows PowerShell para StorSimple para administrar o seu dispositivo

## <a name="overview"></a>Descrição geral

O Windows PowerShell para StorSimple fornece uma interface de linha de comando que pode utilizar para gerir o seu dispositivo Microsoft Azure StorSimple. Como o nome sugere, é uma interface baseada em windows PowerShell, linha de comando que é construída num espaço de execução restrito. Do ponto de vista do utilizador na linha de comando, um espaço de execução limitado aparece como uma versão restrita do Windows PowerShell. Apesar de manter algumas das capacidades básicas do Windows PowerShell, esta interface possui cmdlets adicionais dedicados que estão vocacionados para a gestão do seu dispositivo Microsoft Azure StorSimple.

Este artigo descreve o Windows PowerShell para funcionalidades StorSimple, incluindo como pode ligar-se a esta interface, e contém links para procedimentos passo a passo ou fluxos de trabalho que pode executar usando esta interface. Os fluxos de trabalho incluem como registar o seu dispositivo, configurar a interface de rede no seu dispositivo, instalar atualizações que exijam que o dispositivo esteja em modo de manutenção, alterar o estado do dispositivo e resolver problemas que possa experimentar.

Depois de ler este artigo, poderá:

* Ligue-se ao seu dispositivo StorSimple utilizando o Windows PowerShell para StorSimple.
* Administrar o seu dispositivo StorSimple utilizando o Windows PowerShell para StorSimple.
* Obtenha ajuda no Windows PowerShell para StorSimple.

> [!NOTE]
> * O Windows PowerShell para cmdlets StorSimple permite-lhe gerir o seu dispositivo StorSimple a partir de uma consola em série ou remotamente através do remoting Do Windows PowerShell. Para obter mais informações sobre cada um dos cmdlets individuais que podem ser utilizados nesta interface, consulte [o cmdlet para Windows PowerShell para StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).
> * Os cmdlets Azure PowerShell StorSimple são uma coleção diferente de cmdlets que permitem automatizar tarefas de nível de serviço e migração StorSimple a partir da linha de comando. Para obter mais informações sobre os cmdlets Azure PowerShell para StorSimple, aceda à [referência cmdlet Azure StorSimple](/powershell/module/servicemanagement/azure.service/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


Pode aceder ao Windows PowerShell para StorSimple utilizando um dos seguintes métodos:

* [Ligue-se à consola em série do dispositivo StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Ligue-se remotamente ao StorSimple utilizando o Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Ligue ao Windows PowerShell para StorSimple através da consola em série do dispositivo

Pode [descarregar o software de](https://www.putty.org/) emulação de terminal PuTTY ou similar para ligar ao Windows PowerShell para StorSimple. É necessário configurar o PuTTY especificamente para aceder ao dispositivo Microsoft Azure StorSimple. Os seguintes tópicos contêm passos detalhados sobre como configurar o PuTTy e ligar-se ao dispositivo. São também explicadas várias opções de menu na consola em série.

### <a name="putty-settings"></a>Definições do PuTTY

Certifique-se de que utiliza as seguintes definições de PuTTY para ligar à interface Windows PowerShell a partir da consola em série.

#### <a name="to-configure-putty"></a>Para configurar a PuTTY

1. Na caixa de diálogo de **reconfiguração** PuTTY, no painel **de categorias,** selecione **Teclado**.
2. Certifique-se de que as seguintes opções são selecionadas (estas são as definições predefinidos quando iniciar uma nova sessão).
   
   | Item de teclado | Selecionar |
   | --- | --- |
   | Chave de backspace |Controlo-? (127) |
   | Chaves casa e fim |Standard |
   | Teclas de função e teclado |ESC[n~ |
   | Estado inicial das teclas do cursor |Normal |
   | Estado inicial do teclado numérico |Normal |
   | Ativar funcionalidades extra de teclado |Control-Alt é diferente da AltGr |
   
    ![Configurações de massa suportadas](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Clique em **Aplicar**.
4. No painel **de categorias,** selecione **Tradução**.
5. Na caixa de lista **de caracteres remotos,** selecione **UTF-8**.
6. No **manuseamento de caracteres de desenho de linhas**, selecione **Use Unicode line drawing code points**. A imagem que se segue mostra as seleções corretas do PuTTY.
   
    ![Configurações de massa UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Clique em **Aplicar**.

Agora pode utilizar o PuTTY para ligar à consola em série do dispositivo, fazendo os seguintes passos.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Sobre a consola em série

Quando acede à interface Do Windows PowerShell do seu dispositivo StorSimple através da consola em série, é apresentada uma mensagem de banner, seguida de opções de menu.

A mensagem banner contém informações básicas do dispositivo StorSimple, tais como o modelo, nome, versão de software instalado e o estado do controlador a que está a aceder. A imagem que se segue mostra um exemplo de uma mensagem de banner.

![Mensagem de banner em série](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Pode utilizar a mensagem de banner para identificar se o controlador a que está ligado é _Ativo_ ou _Passivo_.

A imagem a seguir mostra as várias opções de espaço de execução que estão disponíveis no menu da consola em série.

![Registar o seu dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Pode escolher entre as seguintes definições:

1. **Faça login com acesso total** Esta opção permite-lhe ligar (com as credenciais adequadas) ao espaço de funcionação **SSAdminConsole** no controlador local. (O controlador local é o controlador a que está a aceder através da consola em série do seu dispositivo StorSimple.) Esta opção também pode ser usada para permitir que o Microsoft Support aceda a um espaço de funcionaamento sem restrições (uma sessão de suporte) para resolver eventuais problemas com o dispositivo. Depois de utilizar a opção 1 para iniciar sessão, pode permitir que o engenheiro do Microsoft Support aceda a um espaço de funcionamento sem restrições executando um cmdlet específico. Para mais informações, consulte iniciar [uma sessão de suporte](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Faça login no controlador de pares com acesso total** Esta opção é a mesma que a opção 1, exceto que pode ligar (com as credenciais adequadas) ao espaço de funcionação **SSAdminConsole** no controlador de pares. Uma vez que o dispositivo StorSimple é um dispositivo de alta disponibilidade com dois controladores numa configuração activa-passiva, o peer refere-se ao outro controlador do dispositivo a que está a aceder através da consola em série.
   Semelhante à opção 1, esta opção também pode ser usada para permitir que o Microsoft Support aceda a um espaço de funcionação sem restrições num controlador de pares.

3. **Conecte-se com acesso limitado** Esta opção é utilizada para aceder à interface do Windows PowerShell em modo limitado. Não é solicitado para credenciais de acesso. Esta opção liga-se a um espaço de funcionação mais restrito em comparação com as opções 1 e 2.  Algumas das tarefas que estão disponíveis através da opção 1 que **não pode* ser executada neste espaço de funcionação são:
   
   * Reset às definições de fábrica
   * Alterar a palavra-passe
   * Ativar ou desativar o acesso ao suporte
   * Aplicar atualizações
   * Instalar hotfixes

     > [!NOTE]
     > Esta é a opção preferida se tiver esquecido a palavra-passe do administrador do dispositivo e não conseguir ligar através da opção 1 ou 2.

4. **Mudar linguagem** Esta opção permite alterar o idioma de exibição na interface Windows PowerShell. As línguas apoiadas são inglês, japonês, russo, francês, sul-coreano, espanhol, italiano, alemão, chinês e português.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Ligue-se remotamente ao StorSimple utilizando o Windows PowerShell para StorSimple

Pode utilizar o remoing do Windows PowerShell para ligar ao seu dispositivo StorSimple. Quando se ligar desta forma, não verá um menu. (Só se vê um menu se utilizar a consola em série no dispositivo para se ligar. A ligação remota leva-o diretamente ao equivalente a "opção 1 – acesso total" na consola em série.) Com o remoting do Windows PowerShell, liga-se a um espaço de funcionação específico. Também pode especificar o idioma de exibição.

O idioma de exibição é independente do idioma que definiu utilizando a opção **Change Language** no menu de consolas em série. O Remote PowerShell irá recolher automaticamente o local do dispositivo a que se encontra a ligar se não for especificado nenhum.

> [!NOTE]
> Se estiver a trabalhar com anfitriões virtuais microsoft Azure e aparelhos cloud StorSimple, pode utilizar o remoting Windows PowerShell e o anfitrião virtual para ligar ao aparelho em nuvem. Se tiver configurado uma localização partilhada no anfitrião para guardar informações da sessão Windows PowerShell, deve estar ciente de que o principal _de Todos_ inclui apenas utilizadores autenticados. Portanto, se tiver configurado a partilha para permitir o acesso por _Todos_ e se ligar sem especificar credenciais, o principal anónimo não autenticado será utilizado e verá um erro. Para corrigir este problema, no anfitrião de ações tem de ativar a conta do Guest e, em seguida, dar à conta do Hóspede acesso total à partilha ou tem de especificar credenciais válidas juntamente com o cmdlet Windows PowerShell.


Pode utilizar HTTP ou HTTPS para ligar através do remoting Do Windows PowerShell. Utilize as instruções nos seguintes tutoriais:

* [Conecte-se remotamente usando HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Conecte-se remotamente usando HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Considerações de segurança de ligação

Quando estiver a decidir como ligar-se ao Windows PowerShell para storSimple, considere o seguinte:

* A ligação direta à consola em série do dispositivo é segura, mas a ligação à consola em série sobre os interruptores de rede não é. Tenha cuidado com o risco de segurança ao ligar-se à série do dispositivo sobre os interruptores de rede.
* A ligação através de uma sessão HTTP pode oferecer mais segurança do que ligar através da consola em série através da rede. Embora este não seja o método mais seguro, é aceitável em redes fidedignas.
* A ligação através de uma sessão HTTPS é a opção mais segura e recomendada.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrar o seu dispositivo StorSimple utilizando o Windows PowerShell para StorSimple

A tabela que se segue mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser realizados dentro da interface Windows PowerShell do seu dispositivo StorSimple. Para obter mais informações sobre cada fluxo de trabalho, clique na entrada adequada na tabela.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell para fluxos de trabalho StorSimple

| Se quiser fazer isto... | Use este procedimento. |
| --- | --- |
| Registar o seu dispositivo |[Configure e registe o dispositivo utilizando o Windows PowerShell para storSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Configurar proxy Web</br>Ver configurações de procuração web |[Configure o proxy web para o seu dispositivo StorSimple](storsimple-8000-configure-web-proxy.md) |
| Modificar as definições de interface de rede DATA 0 no seu dispositivo |[Modificar a interface de rede DATA 0 para o seu dispositivo StorSimple](storsimple-8000-modify-data-0.md) |
| Parar um controlador </br> Reiniciar ou desligar um controlador </br> Desligue um dispositivo</br>Reset the device to factory default settings (Repor o dispositivo para as predefinições de fábrica) |[Gerir controladores de dispositivos](storsimple-8000-manage-device-controller.md) |
| Instale atualizações e ajustes de modo de manutenção |[Atualizar o seu dispositivo](storsimple-update-device.md) |
| Insira o modo de manutenção </br>Modo de manutenção de saída |[Modos de dispositivo StorSimple](storsimple-8000-device-modes.md) |
| Criar um pacote de suporte</br>Desencriptar e editar um pacote de suporte |[Criar e gerir um pacote de suporte](storsimple-8000-create-manage-support-package.md) |
| Inicie uma sessão de suporte</br> |[Inicie uma sessão de suporte no Windows PowerShell para StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Obtenha ajuda no Windows PowerShell para StorSimple

No Windows PowerShell para StorSimple, a cmdlet Help está disponível. Uma versão online e atualizada desta Ajuda também está disponível, que pode utilizar para atualizar a Ajuda no seu sistema.

Obter Ajuda nesta interface é semelhante ao do Windows PowerShell, e a maioria dos cmdlets relacionados com ajuda funcionará. Pode encontrar ajuda para o Windows PowerShell online: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

Segue-se uma breve descrição dos tipos de Ajuda para esta interface Windows PowerShell, incluindo como atualizar a Ajuda.

### <a name="to-get-help-for-a-cmdlet"></a>Para obter ajuda para um cmdlet

* Para obter ajuda para qualquer cmdlet ou função, utilize o seguinte comando: `Get-Help <cmdlet-name>`
* Para obter ajuda on-line para qualquer cmdlet, utilize o cmdlet anterior com o `-Online` parâmetro: `Get-Help <cmdlet-name> -Online`
* Para obter ajuda total, pode utilizar o `–Full` parâmetro e, por exemplo, utilizar o `–Examples` parâmetro.

### <a name="to-update-help"></a>Para atualizar a Ajuda

Pode atualizar facilmente a Ajuda na interface Windows PowerShell. Execute os seguintes passos para atualizar a Ajuda no seu sistema.

#### <a name="to-update-cmdlet-help"></a>Para atualizar a ajuda do cmdlet
1. Iniciar o Windows PowerShell com a **opção executar como** administrador.
2. Na solicitação de comando, escreva:  `Update-Help`
3. Os ficheiros de ajuda atualizados serão instalados.
4. Depois de instalados os ficheiros de ajuda, escreva: `Get-Help Get-Command` . Isto apresentará uma lista de cmdlets para os quais a Ajuda está disponível.

> [!NOTE]
> Para obter uma lista de todos os cmdlets disponíveis num espaço de execução, inicie sessão na opção de menu correspondente e execute o `Get-Command` cmdlet.


## <a name="next-steps"></a>Passos seguintes

Se tiver algum problema com o seu dispositivo StorSimple ao executar um dos fluxos de trabalho acima referidos, consulte [ferramentas para resolver problemas de implementações StorSimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).