---
title: Administração da IU da Web local do Microsoft Azure Data Box | Microsoft Docs em dados
description: Descreve como utilizar a IU da Web local para administrar o dispositivo do Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 02/01/2019
ms.author: alkohli
ms.openlocfilehash: d5b6949930161f78c6edf4213df98471e96b1d42
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404047"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>Utilizar a IU da Web local para administrar o seu Azure Data Box

Este artigo descreve algumas das tarefas de configuração e gestão que podem ser executadas no Azure Data Box. Pode gerir o Azure Data Box através da IU do portal do Azure e da IU de Web local do dispositivo. Este artigo aborda em especial as tarefas que pode efetuar com a IU de Web local.

A IU de Web local do Azure Data Box é utilizada para a configuração inicial do dispositivo. Também pode utilizar a IU de Web local para encerrar ou reiniciar o Data Box, executar testes de diagnóstico, atualizar software, ver registos de cópia e gerar um pacote de registo para o Suporte da Microsoft.

Este artigo inclui os seguintes tutoriais:

- Gerar um pacote de Suporte
- Encerrar ou reiniciar o dispositivo
- Baixe o BOM ou os arquivos de manifesto
- Ver a capacidade disponível do dispositivo
- Ignorar a validação da soma de verificação 

## <a name="generate-support-package"></a>Gerar Pacote de suporte

Se surgir qualquer problema com o dispositivo, pode criar um Pacote de suporte a partir dos registos do sistema. A equipa de Suporte da Microsoft utiliza este pacote para resolver o problema. Para gerar um Pacote de suporte, execute os passos seguintes:

1. Na IU de Web local, aceda a **Contactar o Suporte** e clique em **Criar Pacote de suporte**.

    ![Criar Pacote de suporte 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. Um Pacote de suporte é recolhido. Esta operação poderá demorar alguns minutos.

    ![Criar Pacote de suporte 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. Depois de concluída a criação do Pacote de suporte, clique em **Transferir pacote de suporte**. 

    ![Criar Pacote de suporte 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. Procure e selecione a localização de transferência. Abra a pasta para ver os conteúdos.

    ![Criar Pacote de suporte 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>Encerrar ou reiniciar o dispositivo

Pode encerrar ou reiniciar o Data Box com a IU de Web local. Recomendamos que antes de reiniciar, coloque as partilhas offline no anfitrião e, em seguida, no dispositivo. Isto minimiza qualquer possibilidade de danos em dados. Certifique-se de que a cópia de dados não está em curso quando encerra o dispositivo.

Para encerrar o Data Box, execute os passos seguintes.

1. Na IU de Web local, aceda a **Encerrar ou reiniciar**.
2. Clique em **Encerrar**.

    ![Encerrar Data Box 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Quando lhe for pedida a confirmação, clique em **OK** para continuar.

    ![Encerrar o Data Box 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Depois de o dispositivo ser encerrado, utilize o botão para ligar/desligar no painel frontal para o ativar.

Para reiniciar o Data Box, execute os passos seguintes.

1. Na IU de Web local, aceda a **Encerrar ou reiniciar**.
2. Clique em **Reiniciar**.

    ![Reiniciar Data Box 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Quando lhe for pedida a confirmação, clique em **OK** para continuar.

   O dispositivo é desligado e, em seguida, reinicia-se.

## <a name="download-bom-or-manifest-files"></a>Baixe o BOM ou os arquivos de manifesto

A fatura de materiais (LM) ou os arquivos de manifesto contém a lista dos arquivos que são copiados para o Data Box. Estes ficheiros são gerados quando preparar o Data Box para envio.

Antes de começar, certifique-se de que o Data Box foi concluída **preparação para envio** passo. Siga estes passos para transferir o BOM ou os arquivos de manifesto:

1. Vá para a IU da web local para o Data Box. Verá que o Data Box foi concluída a preparação para envio. Quando a preparação do dispositivo estiver concluída, o estado do dispositivo é apresentado como **pronto para enviar**.

    ![Dispositivo pronto para enviar](media/data-box-portal-admin/ready-to-ship.png)

2. Clique em **lista de transferência de ficheiros** para transferir a lista de ficheiros que foram copiados em sua caixa de dados.

    ![Clique em lista de transferência de ficheiros](media/data-box-portal-admin/download-list-of-files.png)

3. No Explorador de ficheiros, irá ver essa lista separada dos ficheiros são gerados consoante o protocolo utilizado para ligar ao dispositivo e o tipo de armazenamento do Azure utilizado.

    ![Ficheiros para o protocolo de tipo e a ligação de armazenamento](media/data-box-portal-admin/files-storage-connection-type.png)

   A tabela seguinte mapeia os nomes de ficheiro para o tipo de armazenamento do Azure e o protocolo de ligação utilizado.

    |Nome de ficheiro  |Tipo de armazenamento do Azure  |Protocolo de ligação utilizado |
    |---------|---------|---------|
    |databoxe2etest_BlockBlob.txt     |Blobs de bloco         |SMB/NFS         |
    |databoxe2etest_PageBlob.txt     |Blobs de páginas         |SMB/NFS         |
    |databoxe2etest_AzFile-BOM.txt    |Ficheiros do Azure         |SMB/NFS         |
    |databoxe2etest_PageBlock_Rest-BOM.txt     |Blobs de páginas         |REST        |
    |databoxe2etest_BlockBlock_Rest-BOM.txt    |Blobs de bloco         |REST         |
    |mydbmdrg1_MDisk-BOM.txt    |Disco gerido         |SMB/NFS         |
    |mydbmdrg2_MDisk-BOM.txt     |Disco gerido         |SMB/NFS         |

Utilize esta lista para verificar os ficheiros carregados para a conta de armazenamento do Azure, após o Data Box devolve para o datacenter do Azure. Um arquivo de manifesto de exemplo é mostrado abaixo.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

Este ficheiro contém a lista de todos os ficheiros que foram copiados no Data Box. Nesse arquivo, *crc64* se relaciona com o valor para a soma de verificação gerada para o ficheiro correspondente.

## <a name="view-available-capacity-of-the-device"></a>Ver a capacidade disponível do dispositivo

Pode utilizar o dashboard do dispositivo para ver a capacidade disponível e a utilizada do dispositivo. 

1. Na IU de Web local, aceda a **Ver o dashboard**.
2. Em **Ligar e copiar**, é mostrado o espaço livre e utilizado no dispositivo.

    ![Ver capacidade disponível](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>Ignorar a validação da soma de verificação

As somas de verificação são geradas para os seus dados por predefinição, quando a preparação para envio. Em certos casos raros, dependendo do tipo de dados (tamanhos de ficheiro pequeno), o desempenho pode ser lento. Nesses casos, pode ignorar a soma de verificação. 

Recomendamos vivamente que não desative a soma de verificação, a não ser que o desempenho tenha sido bastante afetado.

1. No canto superior direito da web local da interface do Usuário do seu dispositivo, aceda às definições.

    ![Desativar a soma de verificação](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Desativar** validação da soma de verificação
3. Clique em **Aplicar**.

## <a name="next-steps"></a>Passos Seguintes

- Aprenda como [Gerir o Data Box através do portal do Azure](data-box-portal-admin.md).

