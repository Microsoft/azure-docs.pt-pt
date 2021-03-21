---
title: Rastrear e registar Azure Data Box, Azure Data Box Eventos pesados para encomenda de exportação| Microsoft Docs
description: Descreve como rastrear e registar eventos nas várias fases da sua caixa de dados Azure e da encomenda de exportação pesada da Caixa de Dados Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 3a915ac8de83a5e183660ec4a3d05044eafff4a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94337513"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Rastreio e registo de eventos para a sua Caixa de Dados Azure e Caixa de Dados Azure Encomendas de exportação pesadas

Uma Caixa de Dados ou Caixa de Dados A ordem de exportação pesada passa pelos seguintes passos: encomenda, configuração, cópia de dados, devolução e apagamento de dados. Correspondendo a cada passo da encomenda, pode tomar múltiplas ações para controlar o acesso à encomenda, auditar os eventos, rastrear a encomenda e interpretar os vários registos que são gerados.

Este artigo descreve em detalhe os vários mecanismos ou ferramentas disponíveis para rastrear e auditar encomendas de exportação de Data Box ou Data Box Heavy. A informação neste artigo aplica-se a ambos, Data Box e Data Box Heavy. Nas secções seguintes, quaisquer referências à Caixa de Dados também se aplicam à Data Box Heavy.

O quadro seguinte mostra um resumo das etapas da encomenda de exportação da Caixa de Dados e as ferramentas disponíveis para acompanhar e auditar a encomenda durante cada etapa.

| Fase de encomenda de exportação da Caixa de Dados       | Ferramenta para acompanhar e auditar                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Criar encomenda               | [Configurar o controlo de acesso na encomenda via Azure RBAC](#set-up-access-control-on-the-order) <br> [Ativar o registo verboso na ordem](#enable-verbose-log-in-the-order)                                                    |
| Encomenda processada            | [Acompanhe a encomenda](#track-the-order) através <ul><li> Portal do Azure </li><li> Site da transportadora de envio </li><li>Notificações por e-mail</ul> |
| Configurar dispositivo              | Acesso de credenciais de dispositivo registado em [registos de atividade](#query-activity-logs-during-setup)              |
| Cópia de dados do dispositivo        | [Rever registos de cópias](#copy-log) <br> [Reveja os registos verbose](#verbose-log) antes de copiar dados            |
| Apagamento de dados do dispositivo   | [Ver cadeia de registos de custódia,](#get-chain-of-custody-logs-after-data-erasure) incluindo registos de auditoria e histórico de encomendas                |


## <a name="set-up-access-control-on-the-order"></a>Configurar o controlo de acesso na ordem

Pode controlar quem pode aceder à sua encomenda quando a encomenda é criada pela primeira vez. Configurar funções Azure em vários âmbitos para controlar o acesso à ordem Caixa de Dados. Uma função Azure determina o tipo de acesso – ler- escrever, ler, ler para um subconjunto de operações.

As duas funções que podem ser definidas para o serviço Azure Data Box são:

- **Data Box Reader** - tenha acesso apenas de leitura a uma(s) ordem(s) definida pelo âmbito. Só podem ver detalhes de uma ordem. Não podem aceder a quaisquer outros detalhes relacionados com contas de armazenamento ou editar os detalhes da encomenda, como endereço e assim por diante.
- **Data Box Contributor** - só pode criar uma ordem para transferir dados para uma determinada conta de armazenamento *se já tiverem acesso a uma conta de armazenamento*. Se não tiverem acesso a uma conta de armazenamento, nem sequer podem criar uma ordem de Caixa de Dados para copiar dados para a conta. Esta função não define quaisquer permissões relacionadas com a conta de Armazenamento nem concede acesso a contas de armazenamento.  

Para restringir o acesso a uma encomenda, pode:

- Atribua um papel a um nível de encomenda. O utilizador só tem essas permissões, tal como definidas pelas funções para interagir apenas com essa encomenda específica da Caixa de Dados e nada mais.
- Atribuir uma função ao nível do grupo de recursos, o utilizador tem acesso a todas as encomendas da Caixa de Dados dentro de um grupo de recursos.

Para obter mais informações sobre a utilização sugerida do Azure RBAC, consulte [as melhores práticas para o Azure RBAC](../role-based-access-control/best-practices.md).

## <a name="enable-verbose-log-in-the-order"></a>Ativar o registo verboso na ordem

Ao fazer uma encomenda de exportação para Data Box, tem a opção de ativar a recolha de um registo verboso. Aqui está o ecrã de encomenda onde pode ativar o registo verboso:

![Selecione opção de exportação](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

Quando seleciona a opção **de registo verboso,** é gerado um ficheiro de registo verboso ao copiar os dados da sua conta de Armazenamento Azure. Este registo contém uma lista de todos os ficheiros que foram exportados com sucesso.

Para obter mais informações sobre a ordem de exportação, consulte [Criar uma ordem de exportação para caixa de dados](data-box-deploy-export-ordered.md)

## <a name="track-the-order"></a>Controlar a encomenda

Pode rastrear o seu pedido através do portal Azure e através do site da transportadora. Estão em vigor os seguintes mecanismos para rastrear a encomenda da Caixa de Dados a qualquer momento:

- Para rastrear a encomenda quando o dispositivo estiver no datacenter Azure ou nas suas instalações, aceda à **sua encomenda de Caixa de Dados > Visão Geral** no portal Azure.

    ![Ver estado da ordem e não rastrear](media/data-box-logs/overview-view-status-1.png)

- Para acompanhar a encomenda enquanto o dispositivo está em trânsito, aceda ao site da transportadora regional, por exemplo, ao site da UPS nos EUA. Forneça o número de rastreio associado à sua encomenda.
- A Data Box também envia notificações por e-mail sempre que o estado da encomenda muda com base nos e-mails fornecidos quando a encomenda foi criada. Para obter uma lista de todos os estados de encomenda da Caixa de Dados, consulte [o estado da ordem de visualização](data-box-portal-admin.md#view-order-status). Para alterar as definições de notificação associadas à encomenda, consulte [os detalhes da notificação de Editar](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Registos de atividade de consulta durante a configuração

- A sua Caixa de Dados chega às suas instalações num estado fechado. Pode utilizar as credenciais do dispositivo disponíveis no portal Azure para a sua encomenda.  

    Quando uma Caixa de Dados é configurada, poderá ser necessário saber a quem todos acederam às credenciais do dispositivo. Para descobrir quem acedeu à lâmina **de credenciais** do Dispositivo, pode consultar os registos de Atividade.  Qualquer ação que envolva aceder a detalhes do Dispositivo > lâmina **de credenciais** é registada nos registos de atividade como `ListCredentials` ação.

    ![Registos de Atividades de Consulta](media/data-box-logs/query-activity-log-1.png)

- Cada sinal na Caixa de Dados é registado em tempo real. No entanto, esta informação só está disponível nos [registos de auditoria da Cadeia de Custódia](#chain-of-custody-audit-logs) após a conclusão da encomenda.

## <a name="view-logs-during-data-copy"></a>Ver registos durante a cópia de dados

Antes de copiar dados da sua Caixa de Dados, pode descarregar e rever *o registo de cópias* e o *registo verboso* para os dados que foram copiados para a Caixa de Dados. Estes registos são gerados quando os dados são copiados da sua conta de Armazenamento em Azure para a sua Caixa de Dados. 

### <a name="copy-log"></a>Registo de cópias

Antes de copiar os dados da sua Caixa de Dados, descarregue o registo de cópias a partir da página **'Ligar' e copiar.**

Aqui está uma saída de amostra de registo de *cópia* quando não houve erros e todos os ficheiros foram copiados durante a cópia de dados do dispositivo Azure para data box.

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
Aqui está uma saída de amostra quando o registo de *cópia* sofre erros e alguns dos ficheiros não copiaram do Azure.

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

Tem as seguintes opções para exportar esses ficheiros: 

- Pode transferir os ficheiros que não foi possível copiar através da rede. 
- Se o tamanho dos dados for superior à capacidade utilizável do dispositivo, ocorre uma cópia parcial e todos os ficheiros que não foram copiados são listados neste registo. Pode utilizar este registo como XML de entrada para criar uma nova encomenda do Data Box e, em seguida, copiar esses ficheiros.

### <a name="verbose-log"></a>Log verbose

O *registo verboso* contém uma lista de todos os ficheiros exportados com êxito da conta do Armazenamento do Azure. O registo também contém o tamanho dos ficheiros e o cálculo da soma de verificação.

O registo verbose tem as informações no seguinte formato:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Aqui está uma amostra de saída do tronco verboso. 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

Os registos verbosos também são copiados para a conta de armazenamento Azure. Por predefinição, os registos são escritos num contentor chamado `copylog` . Os registos são armazenados com a seguinte convenção de nomeação:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

O caminho do registo de cópia também é apresentado na lâmina **de visão geral** do portal.

<!-- add a screenshot-->

Pode utilizar estes registos para verificar se os ficheiros copiados do Azure correspondem aos dados que foram copiados para o seu servidor no local. 

Utilize o seu ficheiro de registo verboso:

- Para verificar contra os nomes reais e o número de ficheiros que foram copiados da Caixa de Dados.
- Para verificar contra os tamanhos reais dos ficheiros.
- Para verificar se o *crc64* corresponde a uma corda não-zero. Durante a exportação da Azure é efetuada uma verificação cíclica de redundância .com uma verificação cíclica. Os CRCs da exportação e após os dados serem copiados da Caixa de Dados para o servidor no local podem ser comparados. Uma incompatibilidade de CRC indica que os ficheiros correspondentes não copiaram corretamente.


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Obtenha registos de cadeia de custódia após o apagamento dos dados

Após os dados serem apagados dos discos Data Box de acordo com as diretrizes da Revisão 1 do NIST SP 800-88, a cadeia de registos de custódia está disponível. Estes registos incluem a cadeia de registos de auditoria da custódia e o histórico de encomendas. Os ficheiros BOM ou manifesto também são copiados com os registos de auditoria.

### <a name="chain-of-custody-audit-logs"></a>Registos de auditoria da cadeia de custódia

Os registos de auditoria da cadeia de custódia contêm informações sobre alimentação e acesso a ações na Caixa de Dados ou Caixa de Dados Pesada quando está fora do datacenter Azure. Estes registos estão localizados em: `storage-account/azuredatabox-chainofcustodylogs`

Aqui está uma amostra do registo de auditoria de uma Caixa de Dados:

```output
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

O histórico de encomendas está disponível no portal Azure. Se a encomenda estiver completa e a limpeza do dispositivo (apagamento de dados dos discos) estiver concluída, então vá à ordem do seu dispositivo e navegue para **os detalhes da Encomenda**. A opção **Transferir histórico de encomendas** está disponível. Para mais informações, consulte [o histórico de encomendas de download.](data-box-portal-admin.md#download-order-history)

Se percorrer o histórico da ordem, verá:

- Informação de rastreio do seu dispositivo.
- Eventos com atividade *SecureErase.* Estes eventos correspondem ao apagamento dos dados no disco.
- Links de registo de caixa de dados. São apresentados os caminhos dos registos de *auditoria,* *registos de cópias* e ficheiros *BOM.*

Aqui está uma amostra do registo de histórico de encomendas do portal Azure:

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

- Saiba como [resolver problemas na sua Caixa de Dados e Caixa de Dados Pesada](data-box-troubleshoot.md).
