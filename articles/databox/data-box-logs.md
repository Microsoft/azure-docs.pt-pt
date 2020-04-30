---
title: Track and log Azure Data Box, Azure Data Box Eventos pesados Microsoft Docs
description: Descreve como acompanhar e registar eventos nas várias fases da sua caixa de dados Azure e da caixa de dados Azure Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 74d38af4a64a184b26bd6ba1105db0d2530d8ba6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676415"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Rastreio e registo de eventos para a sua Caixa de Dados Azure e Caixa de Dados Azure Heavy

Uma Caixa de Dados ou Caixa de Dados A encomenda pesada passa pelos seguintes passos: encomenda, configuração, cópia de dados, devolução, upload para Azure e verificação, e apagamento de dados. Correspondendo a cada passo da ordem, pode tomar múltiplas ações para controlar o acesso à ordem, auditar os eventos, acompanhar a ordem e interpretar os vários registos que são gerados.

O quadro seguinte apresenta um resumo das etapas de encomenda pesadas da Caixa de Dados ou caixa de dados e das ferramentas disponíveis para rastrear e auditar a encomenda durante cada passo.

| Fase da encomenda da Caixa de Dados       | Ferramenta para rastrear e auditar                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Criar encomenda               | [Configurar o controlo de acesso na encomenda via RBAC](#set-up-access-control-on-the-order)                                                    |
| Encomenda processada            | [Acompanhe a encomenda](#track-the-order) <ul><li> Portal do Azure </li><li> Website da transportadora de envio </li><li>Notificações por e-mail</ul> |
| Configurar dispositivo              | Credenciais de dispositivo acesso a [registos](#query-activity-logs-during-setup) de atividade                                              |
| Cópia de dados para dispositivo        | [Ver *ficheiros error.xml* ](#view-error-log-during-data-copy) para cópia de dados                                                             |
| Preparar para enviar            | [Inspecione os ficheiros BOM](#inspect-bom-during-prepare-to-ship) ou os ficheiros manifestos no dispositivo                                      |
| Envio de dados para Azure       | [Reveja os registos](#review-copy-log-during-upload-to-azure) de cópias para erros durante o upload de dados no Centro de Dados Do Azure                         |
| Apagamento de dados do dispositivo   | [Ver cadeia de registos de custódia,](#get-chain-of-custody-logs-after-data-erasure) incluindo registos de auditoria e histórico de encomendas                |

Este artigo descreve detalhadamente os vários mecanismos ou ferramentas disponíveis para rastrear e auditar data box ou data box heavy encomenda. As informações deste artigo aplicam-se a ambos, Data Box e Data Box Heavy. Nas secções seguintes, quaisquer referências à Data Box também se aplicam à Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Configurar o controlo de acesso na ordem

Pode controlar quem pode aceder à sua encomenda quando a encomenda for criada pela primeira vez. Configurar funções de Controlo de Acesso (RBAC) baseadas em funções em vários âmbitos para controlar o acesso à ordem data box. Uma função RBAC determina o tipo de acesso – ler-escrever, ler apenas, ler-escrever para um subconjunto de operações.

As duas funções que podem ser definidas para o serviço Azure Data Box são:

- **Data Box Reader** - tenha acesso apenas a uma ou uma ordem definida pelo âmbito. Só podem ver detalhes de uma ordem. Não podem aceder a quaisquer outros detalhes relacionados com contas de armazenamento ou editar os detalhes da encomenda, tais como endereço e assim por diante.
- **Data Box Contributor** - só pode criar uma ordem de transferência de dados para uma determinada conta de armazenamento *se já tiverem acesso a uma conta*de armazenamento . Se não tiverem acesso a uma conta de armazenamento, nem sequer podem criar uma ordem data box para copiar dados para a conta. Esta função não define quaisquer permissões relacionadas com a conta de Armazenamento nem concede acesso a contas de armazenamento.  

Para restringir o acesso a uma encomenda, pode:

- Atribuir um papel a nível de encomenda. O utilizador só tem essas permissões definidas pelas funções para interagir apenas com essa ordem específica da Caixa de Dados e nada mais.
- Atribuir uma função ao nível do grupo de recursos, o utilizador tem acesso a todas as encomendas da Caixa de Dados dentro de um grupo de recursos.

Para obter mais informações sobre a utilização sugerida do RBAC, consulte [as melhores práticas para o Azure RBAC](../role-based-access-control/best-practices.md).

## <a name="track-the-order"></a>Controlar a encomenda

Pode acompanhar a sua encomenda através do portal Azure e através do site da transportadora de transporte marítimo. Estão em vigor os seguintes mecanismos para acompanhar a ordem da Caixa de Dados a qualquer momento:

- Para acompanhar a encomenda quando o dispositivo estiver no Centro de Dados Azure ou nas suas instalações, vá à sua encomenda da Caixa de **Dados > visão geral** no portal Azure.

    ![Ver estado de encomenda e rastrear não](media/data-box-logs/overview-view-status-1.png)

- Para acompanhar a encomenda enquanto o dispositivo está em trânsito, vá ao site da transportadora regional, por exemplo, no site da UPS nos EUA. Forneça o número de rastreio associado à sua encomenda.
- A Data Box também envia notificações por e-mail sempre que o estado da encomenda muda com base nos e-mails fornecidos quando a encomenda foi criada. Para obter uma lista de todos os estados de encomenda da Caixa de Dados, consulte o [estado da encomenda .](data-box-portal-admin.md#view-order-status) Para alterar as definições de notificação associadas à encomenda, consulte os detalhes da [notificação da Edit](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Registos de atividade de consulta durante a configuração

- A sua Caixa de Dados chega às suas instalações em estado fechado. Pode utilizar as credenciais do dispositivo disponíveis no portal Azure para a sua encomenda.  

    Quando uma Caixa de Dados é configurada, poderá ser necessário saber quem acedeu às credenciais do dispositivo. Para descobrir quem acedeu à lâmina de **credenciais do Dispositivo,** pode consultar os registos de Atividade.  Qualquer ação que envolva aceder aos detalhes do Dispositivo > a `ListCredentials` lâmina **de credenciais** é registada nos registos de atividade como ação.

    ![Registos de Atividades de Consulta](media/data-box-logs/query-activity-log-1.png)

- Cada sinal na Caixa de Dados está registado em tempo real. No entanto, estas informações só estão disponíveis nos [registos](#audit-logs) da Auditoria depois de a encomenda ter sido concluída com sucesso.

## <a name="view-error-log-during-data-copy"></a>Ver registo de erros durante a cópia de dados

Durante a cópia de dados para Data Box ou Data Box Heavy, um ficheiro de erro é gerado se houver algum problema com os dados a serem copiados.

### <a name="errorxml-file"></a>Error.xml ficheiro

Certifique-se de que os trabalhos de cópia terminaram sem erros. Se houver erros durante o processo de cópia, descarregue os registos da página **Connect e copy.**

- Se copiou um ficheiro que não é 512 bytes alinhados com uma pasta de disco gerida na sua Caixa de Dados, o ficheiro não é carregado como blob de página para a sua conta de armazenamento de encenação. Verá um erro nos registos. Retire o ficheiro e copie um ficheiro que esteja alinhado 512 bytes.
- Se copiou um VHDX, ou um VHD dinâmico, ou um VHD de disferência (estes ficheiros não são suportados), verá um erro nos registos.

Aqui está uma amostra do *erro.xml* para diferentes erros ao copiar para discos geridos.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Aqui está uma amostra do *erro.xml* para diferentes erros ao copiar para as bolhas de página.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Aqui está uma amostra do *erro.xml* para diferentes erros ao copiar para bloquear bolhas.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Aqui está uma amostra do *erro.xml* para diferentes erros ao copiar para Ficheiros Azure.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

Em cada um dos casos acima referidos, resolva os erros antes de passar para o próximo passo. Para obter mais informações sobre os erros recebidos durante a cópia de dados para data Box através de protocolos SMB ou NFS, vá a Problemas de [Data Box e Data Box Problemas pesados](data-box-troubleshoot.md). Para obter informações sobre erros recebidos durante a cópia de dados para Data Box via REST, vá a problemas de armazenamento de Caixas de [Dados De Sufoco](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Inspecione bom durante a preparação para o navio

Durante a preparação para o navio, é criada uma lista de ficheiros conhecidos como A Nota de Materiais (BOM) ou ficheiro manifesto.

- Utilize este ficheiro para verificar os nomes reais e o número de ficheiros que foram copiados para a Caixa de Dados.
- Utilize este ficheiro para verificar os tamanhos reais dos ficheiros.
- Verifique se o *crc64* corresponde a uma corda não nula. <!--A null value for crc64 indicates that there was a reparse point error)-->

Para obter mais informações sobre os erros recebidos durante a preparação para o navio, vá a problemas de Caixas de Dados de Resolução de [Problemas e Data Box.](data-box-troubleshoot.md)

### <a name="bom-or-manifest-file"></a>BOM ou arquivo manifesto

O ficheiro BOM ou manifesto contém a lista de todos os ficheiros que são copiados para o dispositivo Data Box. O ficheiro BOM tem nomes de ficheiros e os tamanhos correspondentes, bem como a verificação. É criado um ficheiro BOM separado para as bolhas de bloco, bolhas de página, Ficheiros Azure, para cópia através das APIs REST, e para a cópia gerir discos na Caixa de Dados. Pode descarregar os ficheiros BOM a partir da Web UI local do dispositivo durante o preparar para o navio.

Estes ficheiros também residem no dispositivo Data Box e são enviados para a conta de armazenamento associada no centro de dados Azure.

### <a name="bom-file-format"></a>Formato de ficheiro BOM

O BOM ou o ficheiro manifesto tem o seguinte formato geral:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Aqui está uma amostra de um manifesto gerado quando os dados foram copiados para a partilha de blocos blob na Caixa de Dados.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

O BOM ou os ficheiros manifestos também são copiados para a conta de armazenamento Azure. Pode utilizar o BOM ou os ficheiros manifestos para verificar se os ficheiros enviados para o Azure correspondem aos dados copiados para a Caixa de Dados.

## <a name="review-copy-log-during-upload-to-azure"></a>Reveja o registo de cópias durante o upload para o Azure

Durante o upload de dados para o Azure, é criado um registo de cópias.

### <a name="copy-log"></a>Registo de cópias

Para cada encomenda que é processada, o serviço Data Box cria registo de cópias na conta de armazenamento associada. O registo de cópias tem o número total de ficheiros que foram carregados e o número de ficheiros que errou durante a cópia de dados da Data Box para a sua conta de armazenamento Azure.

Um cálculo de Verificação de Redundância Cíclica (CRC) é feito durante o upload para Azure. Os CRCs da cópia de dados e após o upload de dados são comparados. Uma incompatibilidade crc indica que os ficheiros correspondentes não foram carregados.

Por predefinição, os registos `copylog`são escritos num recipiente chamado . Os registos são armazenados com a seguinte convenção de nomeação:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

O caminho do registo de cópias também é apresentado na lâmina **de visão geral** para o portal.

![Caminho para copiar log na lâmina de visão geral quando concluído](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Upload concluído com sucesso 

A amostra seguinte descreve o formato geral de um registo de cópia para um upload da Caixa de Dados que completou com sucesso:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Upload concluído com erros 

O upload para O Azure também pode estar completo com erros.

![Caminho para copiar log in Overview blade quando concluído com erros](media/data-box-logs/copy-log-path-2.png)

Aqui está um exemplo de um registo de cópia onde o upload completo com erros:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>Upload completo com avisos

O upload para O Azure completa com avisos se os seus dados tivessem nomes de contentores/blob/ficheiros que não se conformassem com as convenções de nomeação do Azure e os nomes fossem modificados para fazer o upload dos dados para o Azure.

![Caminho para copiar log in Overview blade quando concluído com avisos](media/data-box-logs/copy-log-path-3.png)

Aqui está um exemplo de um registo de cópiaonde os contentores que não estavam em conformidade com as convenções de nomeação do Azure foram renomeados durante o upload de dados para o Azure.

Os novos nomes únicos para `DataBox-GUID` contentores encontram-se no formato e os dados do contentor são colocados no novo recipiente renomeado. O registo de cópia especifica o antigo e o novo nome do recipiente para o recipiente.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Aqui está um exemplo de um registo de cópias onde as bolhas ou ficheiros que não estavam em conformidade com as convenções de nomeação do Azure, foram renomeados durante o upload de dados para o Azure. Os novos nomes blob ou arquivos são convertidos para SHA256 digerir caminho relativo para o contentor e são enviados para o caminho com base no tipo de destino. O destino pode ser blocos de bolhas, bolhas de página ou Ficheiros Azure.

O `copylog` especifica o antigo e o novo nome de blob ou arquivo e o caminho em Azure.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Obtenha registos de cadeia de custódia após apagamento de dados

Após a eliminação dos dados dos discos data Box de acordo com as diretrizes nist SP 800-88 Revision 1, a cadeia de registos de custódia está disponível. Estes registos incluem os registos de auditoria e o histórico de encomendas. O BOM ou os ficheiros manifestos também são copiados com os registos de auditoria.

### <a name="audit-logs"></a>Registos de auditoria

Os registos de auditoria contêm informações sobre como ligar e aceder a ações na Caixa de Dados ou Caixa de Dados Pesadaquando está fora do centro de dados Azure. Estes registos estão localizados em:`storage-account/azuredatabox-chainofcustodylogs`

Aqui está uma amostra do registo de auditoria de uma Caixa de Dados:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Transferir histórico de encomendas

O histórico de encomendas está disponível no portal Azure. Se a encomenda estiver completa e a limpeza do dispositivo (apagamento de dados dos discos) estiver completa, em seguida, vá à sua encomenda do dispositivo e navegue para **encomendar detalhes**. A opção **Transferir histórico de encomendas** está disponível. Para mais informações, consulte o [histórico de encomendas de download](data-box-portal-admin.md#download-order-history).

Se percorrero histórico da ordem, verá:

- Informação de rastreio de porta-aviões para o seu dispositivo.
- Eventos com atividade *SecureErase.* Estes eventos correspondem ao apagamento dos dados no disco.
- Links de registo da Caixa de Dados. São apresentados os caminhos para os *registos de auditoria,* *registos de cópias*e *ficheiros BOM.*

Aqui está uma amostra do registo histórico da encomenda do portal Azure:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Passos seguintes

- Saiba como [resolver problemas na sua Caixa](data-box-troubleshoot.md)de Dados e Caixa de Dados Pesado .
