---
title: Criar um pacote de suporte da série StorSimple 8000
description: Saiba como criar, descriptografar e editar um pacote de suporte para seu dispositivo StorSimple da série 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 9ca033f6f786c0142261dafa31b93b71a8b3336a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277063"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Criar e gerenciar um pacote de suporte para a série StorSimple 8000

## <a name="overview"></a>Visão geral

Um pacote de suporte do StorSimple é um mecanismo fácil de usar que coleta todos os logs relevantes para auxiliar Suporte da Microsoft com a solução de problemas de dispositivos StorSimple. Os logs coletados são criptografados e compactados.

Este tutorial inclui instruções passo a passo para criar e gerenciar o pacote de suporte para seu dispositivo StorSimple da série 8000. Se você estiver trabalhando com uma matriz virtual StorSimple, vá para [gerar um pacote de log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Criar um pacote de suporte

Em alguns casos, você precisará criar manualmente o pacote de suporte por meio de Windows PowerShell para StorSimple. Por exemplo:

* Se você precisar remover informações confidenciais dos arquivos de log antes de compartilhar com Suporte da Microsoft.
* Se você estiver tendo dificuldades para carregar o pacote devido a problemas de conectividade.

Você pode compartilhar seu pacote de suporte gerado manualmente com Suporte da Microsoft por email. Execute as etapas a seguir para criar um pacote de suporte no Windows PowerShell para StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Para criar um pacote de suporte no Windows PowerShell para StorSimple

1. Para iniciar uma sessão do Windows PowerShell como administrador no computador remoto que é usado para se conectar ao seu dispositivo StorSimple, digite o seguinte comando:
   
    `Start PowerShell`
2. Na sessão do Windows PowerShell, conecte-se ao console do SSAdmin do seu dispositivo:
   
   1. Na linha de comandos, escreva:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. Na caixa de diálogo que é aberta, insira a senha de administrador do dispositivo. A senha padrão é _password1_.
     
      ![Caixa de diálogo credencial do PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Selecione **OK**.
   4. Na linha de comandos, escreva:
     
      `Enter-PSSession $MS`
3. Na sessão que é aberta, insira o comando apropriado.
   
   * Para compartilhamentos de rede protegidos por senha, digite:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Você será solicitado a fornecer uma senha e uma senha de criptografia (porque o pacote de suporte é criptografado). Um pacote de suporte é então criado na pasta padrão (nome do dispositivo anexado com a data e hora atuais).
   * Para compartilhamentos que não são protegidos por senha, você não precisa do parâmetro `-Credential`. Escreva o seguinte:
     
       `Export-HcsSupportPackage`
     
       O pacote de suporte é criado para ambos os controladores na pasta padrão. O pacote é um arquivo compactado e criptografado que pode ser enviado para Suporte da Microsoft para solução de problemas. Para obter mais informações, consulte [Contact suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Os parâmetros do cmdlet Export-HcsSupportPackage

Você pode usar os parâmetros a seguir com o cmdlet Export-HcsSupportPackage.

| Parâmetro | Obrigatório/Opcional | Descrição |
| --- | --- | --- |
| `-Path` |Obrigatório |Use para fornecer o local da pasta de rede compartilhada na qual o pacote de suporte é colocado. |
| `-EncryptionPassphrase` |Obrigatório |Use para fornecer uma senha para ajudar a criptografar o pacote de suporte. |
| `-Credential` |Opcional |Use para fornecer credenciais de acesso para a pasta de rede compartilhada. |
| `-Force` |Opcional |Use para ignorar a etapa de confirmação de senha de criptografia. |
| `-PackageTag` |Opcional |Use para especificar um diretório em *caminho* no qual o pacote de suporte é colocado. O padrão é [nome do dispositivo]-[data e hora atuais: AAAA-MM-DD-HH-mm-ss]. |
| `-Scope` |Opcional |Especifique como **cluster** (padrão) para criar um pacote de suporte para ambos os controladores. Se você quiser criar um pacote somente para o controlador atual, especifique **controlador**. |

## <a name="edit-a-support-package"></a>Editar um pacote de suporte

Depois de gerar um pacote de suporte, talvez seja necessário editar o pacote para remover informações confidenciais. Isso pode incluir nomes de volume, endereços IP do dispositivo e nomes de backup dos arquivos de log.

> [!IMPORTANT]
> Você só pode editar um pacote de suporte que foi gerado por meio de Windows PowerShell para StorSimple. Não é possível editar um pacote criado no portal do Azure com o serviço Device Manager StorSimple.

Para editar um pacote de suporte antes de carregá-lo no site Suporte da Microsoft, primeiro descriptografe o pacote de suporte, edite os arquivos e criptografe-os novamente. Realize os seguintes passos.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Para editar um pacote de suporte no Windows PowerShell para StorSimple

1. Gere um pacote de suporte conforme descrito anteriormente, em [para criar um pacote de suporte no Windows PowerShell para StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Baixe o script](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente no seu cliente.
3. Importe o módulo do Windows PowerShell. Especifique o caminho para a pasta local na qual você baixou o script. Para importar o módulo, digite:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Todos os arquivos são arquivos *. AES* compactados e criptografados. Para descompactar e descriptografar arquivos, digite:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Observe que as extensões de arquivo reais agora são exibidas para todos os arquivos.
   
    ![Editar pacote de suporte](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Quando a senha de criptografia for solicitada, insira a senha que você usou quando o pacote de suporte foi criado.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Navegue até a pasta que contém os arquivos de log. Como os arquivos de log agora são descompactados e descriptografados, eles terão as extensões de arquivo originais. Modifique esses arquivos para remover qualquer informação específica do cliente, como nomes de volume e endereços IP do dispositivo, e salve os arquivos.
7. Feche os arquivos para compactá-los com gzip e criptografe-os com o AES-256. Isso é para velocidade e segurança na transferência do pacote de suporte em uma rede. Para compactar e criptografar arquivos, insira o seguinte:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Editar pacote de suporte](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Quando solicitado, forneça uma senha de criptografia para o pacote de suporte modificado.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Anote a nova frase secreta para que você possa compartilhá-la com Suporte da Microsoft quando solicitado.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exemplo: editando arquivos em um pacote de suporte em um compartilhamento protegido por senha

O exemplo a seguir mostra como descriptografar, editar e criptografar novamente um pacote de suporte.

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

* Saiba mais sobre as [informações coletadas no pacote de suporte](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Saiba como [usar pacotes de suporte e logs de dispositivo para solucionar problemas de implantação de dispositivo](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Saiba como [usar o serviço de Device Manager do storsimple para administrar seu dispositivo storsimple](storsimple-8000-manager-service-administration.md).

