---
title: Crie um pacote de suporte da série StorSimple 8000
description: Aprenda a criar, desencriptar e editar um pacote de suporte para o seu dispositivo da série StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 9ca033f6f786c0142261dafa31b93b71a8b3336a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277063"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Crie e gerencie um pacote de suporte para a série StorSimple 8000

## <a name="overview"></a>Descrição geral

Um pacote de suporte StorSimple é um mecanismo fácil de usar que recolhe todos os registos relevantes para ajudar o Microsoft Support a resolver problemas com qualquer problema com o dispositivo StorSimple. Os registos recolhidos são encriptados e comprimidos.

Este tutorial inclui instruções passo a passo para criar e gerir o pacote de suporte para o seu dispositivo da série StorSimple 8000. Se estiver a trabalhar com um StorSimple Virtual Array, vá [gerar um pacote](storsimple-ova-web-ui-admin.md#generate-a-log-package)de log .

## <a name="create-a-support-package"></a>Criar um pacote de apoio

Em alguns casos, terá de criar manualmente o pacote de suporte através do Windows PowerShell para o StorSimple. Por exemplo:

* Se precisar de remover informações sensíveis dos seus ficheiros de registo antes de partilhar com o Microsoft Support.
* Se tiver dificuldade em carregar o pacote devido a problemas de conectividade.

Pode partilhar o seu pacote de suporte gerado manualmente com o Microsoft Support por e-mail. Execute os seguintes passos para criar um pacote de suporte no Windows PowerShell para o StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Para criar um pacote de suporte no Windows PowerShell para storSimple

1. Para iniciar uma sessão do Windows PowerShell como administrador no computador remoto que é utilizado para ligar ao seu dispositivo StorSimple, introduza o seguinte comando:
   
    `Start PowerShell`
2. Na sessão do Windows PowerShell, ligue-se à consola SSAdmin do seu dispositivo:
   
   1. Na linha de comandos, escreva:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. Na caixa de diálogo que abre, introduza a palavra-passe do administrador do dispositivo. A palavra-passe padrão é _Password1_.
     
      ![Caixa de diálogo credencial PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Selecione **OK**.
   4. Na linha de comandos, escreva:
     
      `Enter-PSSession $MS`
3. Na sessão que abre, insira o comando apropriado.
   
   * Para partilhas de rede protegidas por palavra-passe, introduza:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Será solicitado para uma palavra-passe e uma palavra-passe de encriptação (porque o pacote de suporte está encriptado). Um pacote de suporte é então criado na pasta predefinida (nome do dispositivo anexado com data e hora atuais).
   * Para ações que não estejam protegidas por `-Credential` palavra-passe, não precisa do parâmetro. Escreva o seguinte:
     
       `Export-HcsSupportPackage`
     
       O pacote de suporte é criado para ambos os controladores na pasta predefinida. O pacote é um ficheiro encriptado e comprimido que pode ser enviado para o Microsoft Support para resolução de problemas. Para mais informações, consulte [contacte](storsimple-8000-contact-microsoft-support.md)o Suporte da Microsoft .

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Os parâmetros de cmdlet Export-HcsSupportPackage

Pode utilizar os seguintes parâmetros com o cmdlet Export-HcsSupportPackage.

| Parâmetro | Obrigatório/Opcional | Descrição |
| --- | --- | --- |
| `-Path` |Necessário |Utilize para fornecer a localização da pasta partilhada da rede na qual o pacote de suporte é colocado. |
| `-EncryptionPassphrase` |Necessário |Utilize para fornecer uma frase-passe para ajudar a encriptar o pacote de suporte. |
| `-Credential` |Opcional |Utilize para fornecer credenciais de acesso para a pasta partilhada pela rede. |
| `-Force` |Opcional |Utilize para saltar o passo de confirmação da frase de encriptação. |
| `-PackageTag` |Opcional |Utilize para especificar um diretório no âmbito do *Caminho* em que o pacote de suporte é colocado. O predefinido é [nome do dispositivo]-[data e hora atual:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Opcional |Especifique como **Cluster** (predefinido) para criar um pacote de suporte para ambos os controladores. Se pretender criar uma embalagem apenas para o controlador atual, especifique o **Controlador**. |

## <a name="edit-a-support-package"></a>Editar um pacote de suporte

Depois de ter gerado um pacote de suporte, poderá ser necessário editar o pacote para remover informações sensíveis. Isto pode incluir nomes de volume, endereços IP do dispositivo e nomes de backup dos ficheiros de registo.

> [!IMPORTANT]
> Só é possível editar um pacote de suporte que foi gerado através do Windows PowerShell para o StorSimple. Não é possível editar um pacote criado no portal Azure com o serviço StorSimple Device Manager.

Para editar um pacote de suporte antes de o enviar no site do Suporte do Microsoft, primeiro desencriptar o pacote de suporte, editar os ficheiros e, em seguida, reencriptar. Realize os seguintes passos.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Para editar um pacote de suporte no Windows PowerShell para storSimple

1. Gere um pacote de suporte como descrito anteriormente, para criar um pacote de [suporte no Windows PowerShell para storSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Descarregue o script](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente no seu cliente.
3. Importar o módulo PowerShell do Windows. Especifique o caminho para a pasta local em que descarregou o script. Para importar o módulo, insira:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Todos os ficheiros são ficheiros *.aes* que são comprimidos e encriptados. Para descomprimir e desencriptar ficheiros, introduza:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Note que as extensões de ficheiros reais estão agora apresentadas para todos os ficheiros.
   
    ![Editar pacote de suporte](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Quando for solicitado para a frase-passe de encriptação, introduza a frase-passe que usou quando o pacote de suporte foi criado.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Navegue na pasta que contém os ficheiros de registo. Como os ficheiros de registo estão agora descomprimidos e desencriptados, estes terão extensões de ficheiros originais. Modifique estes ficheiros para remover quaisquer informações específicas do cliente, tais como nomes de volume e endereços IP do dispositivo, e guarde os ficheiros.
7. Feche os ficheiros para os comprimir com gzip e encripte-os com AES-256. Isto é para rapidez e segurança na transferência do pacote de suporte para uma rede. Para comprimir e encriptar ficheiros, introduza o seguinte:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Editar pacote de suporte](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Quando solicitado, forneça uma palavra-passe de encriptação para o pacote de suporte modificado.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Escreva a nova frase de passe, para que possa partilhá-la com o Microsoft Support quando solicitado.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exemplo: Editar ficheiros num pacote de suporte numa ação protegida por palavra-passe

O exemplo que se segue mostra como desencriptar, editar e encriptar um pacote de suporte.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Passos seguintes

* Conheça as [informações recolhidas no pacote de suporte](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Aprenda a utilizar pacotes de [suporte e registos de dispositivos para perturbar a implementação do dispositivo](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Aprenda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

