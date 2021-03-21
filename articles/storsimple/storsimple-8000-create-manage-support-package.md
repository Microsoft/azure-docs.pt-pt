---
title: Criar um pacote de suporte da série StorSimple 8000
description: Aprenda a criar, desencriptar e editar um pacote de suporte para o seu dispositivo da série StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4a847b273472ecc9d2aaa3993ec9d88aa46f2e7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017173"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Criar e gerir um pacote de suporte para a série StorSimple 8000

## <a name="overview"></a>Descrição geral

Um pacote de suporte StorSimple é um mecanismo de fácil utilização que recolhe todos os registos relevantes para ajudar o Microsoft Support a resolver problemas em quaisquer problemas do dispositivo StorSimple. Os registos recolhidos são encriptados e comprimidos.

Este tutorial inclui instruções passo a passo para criar e gerir o pacote de suporte para o seu dispositivo da série StorSimple 8000. Se estiver a trabalhar com uma Matriz Virtual StorSimple, vá [gerar um pacote de registos](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Criar um pacote de suporte

Em alguns casos, terá de criar manualmente o pacote de suporte através do Windows PowerShell para storSimple. Por exemplo:

* Se precisar de remover informações sensíveis dos seus ficheiros de registo antes de partilhar com o Microsoft Support.
* Se tiver dificuldade em carregar o pacote devido a problemas de conectividade.

Pode partilhar o seu pacote de suporte gerado manualmente com o Microsoft Support por e-mail. Execute os seguintes passos para criar um pacote de suporte no Windows PowerShell para StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Para criar um pacote de suporte no Windows PowerShell para StorSimple

1. Para iniciar uma sessão Do Windows PowerShell como administrador no computador remoto utilizado para ligar ao seu dispositivo StorSimple, insira o seguinte comando:
   
    `Start PowerShell`
2. Na sessão Windows PowerShell, ligue-se à Consola SSAdmin do seu dispositivo:
   
   1. Na linha de comandos, escreva:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. Na caixa de diálogo que abre, introduza a palavra-passe do administrador do dispositivo. A palavra-passe predefinida é _Palavra-passe1_.
     
      ![Caixa de diálogo credencial PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Selecione **OK**.
   4. Na linha de comandos, escreva:
     
      `Enter-PSSession $MS`
3. Na sessão que abre, insira o comando apropriado.
   
   * Para ações de rede protegidas por palavra-passe, insira:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Será solicitado uma palavra-passe e uma palavra-passe de encriptação (porque o pacote de suporte está encriptado). Um pacote de suporte é então criado na pasta predefinida (nome do dispositivo anexado à data e hora atuais).
   * Para ações que não estão protegidas por palavra-passe, não precisa do `-Credential` parâmetro. Escreva o seguinte:
     
       `Export-HcsSupportPackage`
     
       O pacote de suporte é criado para ambos os controladores na pasta predefinidora. O pacote é um ficheiro encriptado e comprimido que pode ser enviado para o Microsoft Support para resolução de problemas. Para obter mais informações, consulte [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Os Export-HcsSupportPackage parâmetros do cmdlet

Pode utilizar os seguintes parâmetros com o Export-HcsSupportPackage cmdlet.

| Parâmetro | Obrigatório/Opcional | Descrição |
| --- | --- | --- |
| `-Path` |Obrigatório |Utilize para fornecer a localização da pasta partilhada na rede na qual o pacote de suporte é colocado. |
| `-EncryptionPassphrase` |Necessário |Utilize para fornecer uma palavra-passe para ajudar a encriptar o pacote de suporte. |
| `-Credential` |Opcional |Utilize para fornecer credenciais de acesso para a pasta partilhada na rede. |
| `-Force` |Opcional |Utilize para saltar o passo de confirmação da palavra-passe de encriptação. |
| `-PackageTag` |Opcional |Utilize para especificar um diretório em *caminho* no qual o pacote de suporte é colocado. O padrão é [nome do dispositivo]-[data e hora corrente:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Opcional |Especifique como **Cluster** (predefinido) para criar um pacote de suporte para ambos os controladores. Se pretender criar um pacote apenas para o controlador atual, especifique **o Controlador**. |

## <a name="edit-a-support-package"></a>Editar um pacote de suporte

Depois de ter gerado um pacote de suporte, poderá ter de editar a embalagem para remover informações sensíveis. Isto pode incluir nomes de volume, endereços IP do dispositivo e nomes de backup dos ficheiros de registo.

> [!IMPORTANT]
> Só é possível editar um pacote de suporte que foi gerado através do Windows PowerShell para storSimple. Não é possível editar um pacote criado no portal Azure com o serviço StorSimple Device Manager.

Para editar um pacote de suporte antes de o enviar no site do Microsoft Support, desencriptar primeiro o pacote de suporte, editar os ficheiros e, em seguida, reencrimá-lo novamente. Realize os seguintes passos.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Para editar um pacote de suporte no Windows PowerShell para StorSimple

1. Gere um pacote de suporte como descrito anteriormente, em [Criar um pacote de suporte no Windows PowerShell para StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Descarregue o script](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente no seu cliente.
3. Importe o módulo Windows PowerShell. Especifique o caminho para a pasta local na qual descarregou o script. Para importar o módulo, insira:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Todos os ficheiros são *ficheiros .aes* que são comprimidos e encriptados. Para descomprimir e desencriptar ficheiros, insira:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Note que as extensões de ficheiros reais são agora apresentadas para todos os ficheiros.
   
    ![Editar pacote de suporte](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Quando for solicitado para a palavra-passe de encriptação, introduza a frase de passe que usou quando o pacote de suporte foi criado.
   
    ```powershell
    cmdlet Open-HcsSupportPackage at command pipeline position 1

    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
6. Navegue na pasta que contém os ficheiros de registo. Como os ficheiros de registo estão agora descomprimidos e desencriptados, estes terão extensões de ficheiros originais. Modifique estes ficheiros para remover qualquer informação específica do cliente, como nomes de volume e endereços IP do dispositivo, e guarde os ficheiros.
7. Feche os ficheiros para comprimi-los com gzip e criptografá-los com AES-256. Isto é para a rapidez e segurança na transferência do pacote de apoio para uma rede. Para comprimir e encriptar ficheiros, introduza o seguinte:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Editar pacote de suporte 2](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Quando solicitado, forneça uma palavra-passe de encriptação para o pacote de suporte modificado.
   
    ```powershell
    cmdlet Close-HcsSupportPackage at command pipeline position 1
    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
9. Escreva a nova palavra-passe, para que possa partilhá-la com o Microsoft Support quando solicitado.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exemplo: Editar ficheiros num pacote de suporte numa partilha protegida por palavra-passe

O exemplo a seguir mostra como desencriptar, editar e reencrimar um pacote de suporte.

```powershell
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
```

## <a name="next-steps"></a>Passos seguintes

* Conheça as [informações recolhidas no pacote De Apoio](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Saiba como [utilizar pacotes de suporte e registos de dispositivos para resolver problemas na implementação do seu dispositivo](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

