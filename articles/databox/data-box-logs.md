---
title: Controlar e inicie a sessão do Azure Data Box, eventos pesadas de caixa de dados do Azure | Documentos da Microsoft
description: Descreve como controlar e registrar eventos em vários estágios do seu pedido do Azure Data Box e pesadas de caixa de dados do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 108d17d3e0ca5f32648f9d4f6cf4b5f9a2984d0c
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495807"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Controlo e o log de eventos para o Azure Data Box e pesadas de caixa de dados do Azure

Uma ordem de caixa de dados ou dados de caixa pesada atravessa os seguintes passos: ordenar, configurar, dados copiam, retornarem, carregar para o Azure e verifique se e eliminação de dados. Correspondente a cada etapa na ordem, pode efetuar várias ações para controlar o acesso ao pedido, os eventos de auditoria, controlar a ordem e interpretar vários registos que são gerados.

A tabela seguinte mostra um resumo dos passos de ordem Data Box ou pesadas de caixa de dados e as ferramentas disponíveis para controlar e auditar a ordem durante cada passo.

| Fase de ordem de caixa de dados       | Ferramenta de controlar e auditar a                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Criar pedido               | [Configurar o controlo de acesso na ordem através do RBAC](#set-up-access-control-on-the-order)                                                    |
| Ordem processadas            | [Controlar a ordem](#track-the-order) através de <ul><li> Portal do Azure </li><li> Web site de operadora de envio </li><li>Notificações por e-mail</ul> |
| Configurar o dispositivo              | Dispositivo credenciais de acesso com sessão iniciado [registos de atividades](#query-activity-logs-during-setup)                                              |
| Cópia de dados para o dispositivo        | [Modo de exibição *error.xml* arquivos](#view-error-log-during-data-copy) para cópia de dados                                                             |
| Preparar para enviar            | [Inspecionar os ficheiros BOM](#inspect-bom-during-prepare-to-ship) ou os arquivos de manifesto no dispositivo                                      |
| Carregamento de dados para o Azure       | [Revisão *copylogs* ](#review-copy-log-during-upload-to-azure) erros durante a dados carregar no datacenter do Azure                         |
| Eliminação de dados do dispositivo   | [Ver cadeia de custódia registos](#get-chain-of-custody-logs-after-data-erasure) incluindo registos de auditoria e histórico de pedidos                                                   |

Este artigo descreve detalhadamente a vários mecanismos ou ferramentas disponíveis para controlar e auditar a ordem de caixa de dados ou dados de caixa pesada. As informações neste artigo aplica-se para tanto, Data Box e dados de caixa pesada. Nas secções seguintes, quaisquer referências a caixa de dados também se aplicam a dados de caixa pesada.

## <a name="set-up-access-control-on-the-order"></a>Configurar o controlo de acesso em ordem

Pode controlar quem pode aceder ao seu pedido quando a ordem é criada. Configure funções de controlo de acesso baseado em funções (RBAC) em vários âmbitos para controlar o acesso para a encomenda do Data Box. Uma função RBAC determina o tipo de acesso – leitura e escrita, só de leitura, leitura e escrita a um subconjunto de operações.

As duas funções que podem ser definidas para o serviço do Azure Data Box são:

- **Leitor de dados de caixa** -têm acesso só de leitura para um order(s) conforme definido pelo escopo. Apenas podem visualizar os detalhes de um pedido. Não podem aceder a quaisquer outros detalhes relacionados com contas de armazenamento ou editar os detalhes da encomenda como o endereço e assim por diante.
- **Contribuinte do Data caixa** -só pode criar um pedido para transferir dados para uma conta de armazenamento fornecida *se já tiver acesso de escrita para uma conta de armazenamento*. Se não tiverem acesso a uma conta de armazenamento, não podem criar até mesmo uma encomenda do Data Box para copiar dados para a conta. Esta função não define qualquer conta de armazenamento relacionada com permissões nem concede acesso a contas de armazenamento.  

Para restringir o acesso a um pedido, pode:

- Atribua uma função num nível de ordem. O usuário precisa apenas essas permissões, conforme definido pelas funções de interagir com apenas essa ordem de Data Box específica e nada mais.
- Atribuir uma função ao nível do grupo de recursos, o utilizador tem acesso a todos os pedidos de caixa de dados dentro de um grupo de recursos.

Para obter mais informações sobre a utilização RBAC sugerida, consulte [melhores práticas do RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Controlar a encomenda

Pode controlar a sua encomenda através do portal do Azure e por meio do site de operadora de envio. Os seguintes mecanismos estão em vigor para controlar a ordem do Data Box em qualquer altura:

- Para controlar a ordem quando o dispositivo estiver no datacenter do Azure ou no local, aceda ao seu **encomenda do Data Box > Descrição geral** no portal do Azure.

    ![Ver o estado da encomenda e não de controlo](media/data-box-logs/overview-view-status-1.png)

- Para controlar a ordem, enquanto o dispositivo está em trânsito, vá até o site de operadora regionais, por exemplo, Web site no-BREAK nos EUA. Fornece o número de controlo associado ao seu pedido.
- Caixa de dados também envia notificações por e-mail sempre que as alterações de estado de ordem com base em mensagens de e-mail fornecidas quando o pedido foi criado. Para obter uma lista de todos os Estados de ordem de Data Box, consulte [ver o estado da encomenda](data-box-portal-admin.md#view-order-status). Para alterar as definições de notificação associadas à ordem, consulte [editar detalhes de notificação](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Registos de atividade de consulta durante a configuração

- O Data Box é recebido no local no estado bloqueado. Pode utilizar as credenciais de dispositivo disponíveis no portal do Azure para a sua encomenda.  

    Quando uma caixa de dados estiver configurada, terá de saber que todos os acedidos as credenciais do dispositivo. Para saber quem acedeu a **credenciais do dispositivo** painel, pode consultar os registos de atividades.  Qualquer ação que envolve o acesso **detalhes do dispositivo > credenciais** painel está registado nos registos de atividades como `ListCredentials` ação.

    ![Registos de Atividades de Consulta](media/data-box-logs/query-activity-log-1.png)

- Cada início de sessão para o Data Box é registado em tempo real. No entanto, estas informações só estão disponíveis na [registos de auditoria](#audit-logs) depois do pedido é concluído com êxito.

## <a name="view-error-log-during-data-copy"></a>Ver o registo de erro durante a cópia de dados

Durante a cópia de dados para a caixa de dados ou dados de caixa pesada, um ficheiro de erro é gerado se existem quaisquer problemas com os dados a ser copiados.

### <a name="errorxml-file"></a>Error.xml file

Certifique-se de que as tarefas de cópia tem concluído sem erros. Se houver erros durante o processo de cópia, transferir os registos a partir da **Connect e a cópia** página.

- Se copiar um ficheiro que é não 512 bytes alinhadas para uma pasta de disco gerido em sua caixa de dados, o ficheiro não está carregado como BLOBs de páginas à sua conta de armazenamento de teste. Verá um erro nos registos. Remova o ficheiro e copie um ficheiro que é de 512 bytes alinhados.
- Se tiver copiado um VHDX, ou de um VHD dinâmico ou de um VHD diferencial (esses arquivos não são suportados), verá um erro nos registos.

Eis um exemplo do *error.xml* erros diferentes quando copiar para discos geridos.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Eis um exemplo do *error.xml* erros diferentes ao copiar para blobs de páginas.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Eis um exemplo do *error.xml* erros diferentes ao copiar para blobs de blocos.

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

Eis um exemplo do *error.xml* erros diferentes ao copiar ficheiros do Azure.

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

Em cada um dos casos acima, resolva os erros antes de prosseguir para o passo seguinte. Para obter mais informações sobre os erros recebidos durante a cópia de dados para Data Box através de protocolos SMB ou NFS, aceda a [problemas de resolver problemas relacionados com o Data Box e dados de caixa pesada](data-box-troubleshoot.md). Para obter informações sobre erros recebidos durante a cópia de dados para o Data Box através de REST, aceda a [problemas de armazenamento de BLOBs de caixa de dados resolver](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Inspecionar BOM durante a preparação para envio

Durante a preparação para envio, uma lista de ficheiros conhecida como a lista de materiais (LM) ou o ficheiro de manifesto é criado.

- Utilize este ficheiro para verificar contra os nomes reais e o número de ficheiros que foram copiados para o Data Box.
- Utilize este ficheiro para verificar contra os tamanhos reais dos ficheiros.
- Certifique-se de que o *crc64* corresponde a uma cadeia de caracteres diferente de zero. <!--A null value for crc64 indicates that there was a reparse point error)-->

Para obter mais informações sobre os erros recebidos durante a preparar para enviar, vá para [problemas de resolver problemas relacionados com o Data Box e dados de caixa pesada](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>BOM ou manifesto do arquivo

O BOM ou o ficheiro de manifesto contém a lista de todos os ficheiros são copiados para o dispositivo do Data Box. O ficheiro BOM tem nomes de ficheiros e os tamanhos correspondentes, bem como a soma de verificação. É criado um ficheiro BOM separado para os blobs de blocos, blobs de páginas, ficheiros do Azure, para obter uma cópia por meio das APIs REST e para a cópia para os managed disks no Data Box. Pode transferir os ficheiros BOM a partir da web local da interface do Usuário do dispositivo durante a preparação para envio.

Além disso, esses arquivos residem no dispositivo do Data Box e são carregados para a conta de armazenamento associado no datacenter do Azure.

### <a name="bom-file-format"></a>Formato de ficheiro BOM

BOM ou manifesto do arquivo tem o seguinte formato geral:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Eis um exemplo de um manifesto gerado quando os dados foram copiados para a partilha de blob de bloco na caixa de dados.

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

O BOM ou arquivos de manifesto também são copiados para a conta de armazenamento do Azure. Pode utilizar a BOM ou para verificar que os ficheiros carregados para o Azure corresponderem aos dados foram copiados para o Data Box, os arquivos de manifesto.

## <a name="review-copy-log-during-upload-to-azure"></a>Consulte o registo de cópia durante o carregamento para o Azure

Durante o carregamento de dados para o Azure, um *copylog* é criado.

### <a name="copylog"></a>Copylog

Para cada ordem processada, o serviço do Data Box cria *copylog* na conta de armazenamento associados. O *copylog* tem o número total de ficheiros que foram carregados e o número de ficheiros que terminou devido a erros durante o dados copiar do Data Box para a sua conta de armazenamento do Azure.

Uma computação de verificação de redundância cíclica (CRC) é feita durante o carregamento para o Azure. CRCs partir da cópia de dados e depois em comparação com o carregamento de dados. Um erro de correspondência CRC indica que os ficheiros correspondentes Falha ao carregar.

Por predefinição, os registos são escritos para um contentor com o nome copylog. Os registos são armazenados com a seguinte convenção de nomenclatura:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

O caminho de copylog também é apresentado no **descrição geral** painel para o portal.

![Caminho para copylog no painel de descrição geral quando concluída](media/data-box-logs/copy-log-path-1.png)

O exemplo a seguir descreve o formato geral de um ficheiro de copylog para um Data Box carregar que foi concluída com êxito:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Carregamento para o Azure também pode concluir com erros.

![Caminho para copylog no painel de descrição geral, quando foi concluído com erros](media/data-box-logs/copy-log-path-2.png)

Eis um exemplo de um copylog em que o carregamento foi concluído com erros:

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


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Obter cadeia de registos de custódia após a eliminação de dados

Depois dos dados são apagados dos discos de dados de caixa de acordo com as diretrizes de revisão 1 NIST SP 800 88, a cadeia de custódia registos estão disponíveis. Estes registos incluem os registos de auditoria e o histórico de pedidos. O BOM ou arquivos de manifesto também são copiados com os registos de auditoria.

### <a name="audit-logs"></a>Registos de auditoria

Registos de auditoria contém informações sobre ativação e partilhar o acesso do Data Box ou dados de caixa pesadas quando ele está fora do datacenter do Azure. Estes registos estão localizados em: `storage-account/azuredatabox-chainofcustodylogs`

Eis um exemplo de log de auditoria numa caixa de dados:

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

Histórico de pedidos está disponível no portal do Azure. Se o pedido for concluído e a limpeza de dispositivo (eliminação de dados dos discos) estiver concluída, em seguida, vá para a sua encomenda do dispositivo e navegue para **detalhes de pedidos**. ** Transferir o histórico de pedidos** opção está disponível. Para obter mais informações, consulte [transferir o histórico de encomendas](data-box-portal-admin.md#download-order-history).

Se percorrer o histórico de pedidos, consulte:

- Operadora de informações para o seu dispositivo de controlo.
- Eventos com *SecureErase* atividade. Esses eventos correspondem para a eliminação de dados no disco.
- Ligações de registo de caixa de dados. Os caminhos para o *registos de auditoria*, *copylogs*, e *BOM* ficheiros são apresentados.

Eis um exemplo do registo de histórico de ordem do portal do Azure:

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

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [resolver problemas no seu Data Box e dados de caixa pesada](data-box-troubleshoot.md).
