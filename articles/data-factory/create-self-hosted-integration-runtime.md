---
title: Criar um integration runtime autoalojado
description: Saiba como criar um tempo de funcionação de integração auto-hospedado na Azure Data Factory, que permite às fábricas de dados aceder em lojas de dados numa rede privada.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 06/18/2019
ms.openlocfilehash: 0d04ea7d7003f274b252e057b7afced7759bfaae
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261219"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Criar e configurar um tempo de execução de integração auto-hospedado

O tempo de integração (IR) é a infraestrutura computacional que a Azure Data Factory utiliza para fornecer capacidades de integração de dados em diferentes ambientes de rede. Para mais detalhes sobre o IR, consulte a visão geral do tempo de [integração.](concepts-integration-runtime.md)

Um tempo de execução de integração auto-hospedado pode executar atividades de cópia entre uma loja de dados em nuvem e uma loja de dados numa rede privada. Também pode despachar atividades de transformação contra recursos de computação numa rede no local ou numa rede virtual Azure. A instalação de um tempo de execução de integração auto-hospedado necessita de uma máquina no local ou de uma máquina virtual dentro de uma rede privada.  

Este artigo descreve como pode criar e configurar um IR auto-hospedado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Criação de um tempo de execução de integração auto-hospedado

Para criar e criar um tempo de execução de integração auto-hospedado, utilize os seguintes procedimentos.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Criar um IR auto-hospedado via Azure PowerShell

1. Pode utilizar o Azure PowerShell para esta tarefa. Segue-se um exemplo:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Descarregue](https://www.microsoft.com/download/details.aspx?id=39717) e instale o tempo de execução de integração auto-hospedado numa máquina local.

3. Recupere a chave de autenticação e registe o tempo de execução da integração auto-hospedado com a chave. Aqui está um exemplo da PowerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Criar um IR auto-hospedado via Azure Data Factory UI

Utilize os seguintes passos para criar um IR auto-hospedado utilizando a Azure Data Factory UI.

1. Na página **Let's start** da Azure Data Factory UI, selecione o separador **Autor** no painel mais à esquerda.

   ![O botão de autor da página inicial](media/doc-common-process/get-started-page-author-button.png)

1. Selecione Ligações na parte inferior do painel mais à esquerda e selecione **tempos** de **integração** na janela **Ligações.** **Selecione +Novo**.

   ![Criar um integration runtime](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. Na janela de configuração do tempo de **integração,** selecione **Executar o movimento de dados e enviar atividades para computas externas**, e selecione **Continuar**.

1. Introduza um nome para o seu IR e selecione **Criar**.

1. Selecione o link em parte da **Opção 1** para abrir a configuração expressa no seu computador. Ou siga os passos da **Opção 2** para configurar manualmente. As seguintes instruções baseiam-se na configuração manual:

   ![Configuração do runtime de integração](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Copiar e colar a chave de autenticação. Selecione Baixar e instalar o tempo de **execução da integração.**

    1. Descarregue o tempo de execução da integração auto-hospedado numa máquina local do Windows. Executar o instalador.

    1. Na página 'Tempo de Execução de Integração do **Registo' (Auto-hospedado),** cola a chave que guardou anteriormente e selecione **Register**.
    
       ![Registar o integration runtime](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Na página do Novo Tempo de **Integração (Auto-hospedado)** do nó, selecione **Finish**.

1. Após o tempo de execução de integração auto-hospedado ser registado com sucesso, você vê a seguinte janela:

    ![Registo com êxito](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Configurar um IR auto-hospedado num VM Azure através de um modelo de Gestor de Recursos Azure

Você pode automatizar configuração de IR auto-hospedada numa máquina virtual Azure utilizando o [modelo de IR auto-hospedeiro Create self host](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). O modelo proporciona uma maneira fácil de ter um IR totalmente funcional auto-hospedado dentro de uma rede virtual Azure. O IR tem funcionalidades de alta disponibilidade e escalabilidade, desde que defina a contagem do nó para 2 ou mais.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Criar um IR auto-hospedado existente através da PowerShell local

Pode utilizar uma linha de comando para configurar ou gerir um IR auto-hospedado existente. Esta utilização pode ajudar especialmente a automatizar a instalação e o registo de nós de INFRAVERMELHOS auto-hospedados.

Dmgcmd.exe está incluído no instalador auto-hospedado. Está tipicamente localizado na pasta C:\Program Files\Microsoft Integration Runtime\3.0\Shared\ Esta aplicação suporta vários parâmetros e pode ser invocada através de uma linha de comando usando scripts de lote para automação.

Utilize a aplicação da seguinte forma:

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

Aqui estão detalhes dos parâmetros e propriedades da aplicação: 

| Propriedade                                                    | Descrição                                                  | Necessário |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **Registro Novo Nóde** "`<AuthenticationKey>`"                     | Registe um nó de tempo de integração auto-hospedado com a chave de autenticação especificada. | Não       |
| **Registro Novo Nóde** "`<AuthenticationKey>`"`<NodeName>`"      | Registe um nó de tempo de integração auto-hospedado com a chave de autenticação especificada e o nome do nó. | Não       |
| **EnableRemoteAccess** "`<port>`"`<thumbprint>`"]            | Ative o acesso remoto no nó atual para criar um cluster de alta disponibilidade. Ou ativar a definição de credenciais diretamente contra o IR auto-hospedado sem passar pela Azure Data Factory. Faça este último utilizando o cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** de uma máquina remota na mesma rede. | Não       |
| **EnableRemoteAccessIncontainer** "`<port>`"`<thumbprint>`"] | Ative o acesso remoto ao nó atual quando o nó correr num recipiente. | Não       |
| **Desativar O Telecomando**                                         | Desative o acesso remoto ao nó atual. É necessário acesso remoto para configuração de multinódos. O **Novo AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell cmdlet ainda funciona mesmo quando o acesso remoto é desativado. Este comportamento é verdadeiro desde que o cmdlet seja executado na mesma máquina que o nó de INFRAVERMELHOS auto-hospedado. | Não       |
| **Chave** "`<AuthenticationKey>`"                                 | Sobrepor ou atualizar a chave de autenticação anterior. Cuidado com esta ação. O seu anterior nó de IR auto-hospedado pode ficar offline se a chave for um novo tempo de funcionação de integração. | Não       |
| **GenerateBackupFile** "`<filePath>`"`<password>`"            | Gere um ficheiro de reserva para o nó atual. O ficheiro de reserva inclui a chave do nó e as credenciais da loja de dados. | Não       |
| **ImportBackupFile** "`<filePath>`"`<password>`"              | Restaure o nó de um ficheiro de reserva.                          | Não       |
| **Reiniciar**                                                     | Reinicie o serviço de hospedar-se em tempo de integração auto-hospedado.   | Não       |
| **Começar**                                                       | Inicie o serviço de hospedar-se em tempo de integração auto-hospedado.     | Não       |
| **Parar**                                                        | Pare o serviço de hospedar-se em tempo de integração auto-hospedado.        | Não       |
| **StartUpgradeService**                                         | Inicie o serviço de upgrade de tempo de integração auto-hospedado.       | Não       |
| **Serviço StopUpgrade**                                          | Pare o serviço de atualização de tempo de integração auto-hospedado.        | Não       |
| **Atualização TurnOnAuto**                                            | Ligue a atualização automática de tempo de execução de integração auto-hospedada.        | Não       |
| **DesativarAutoUpdate**                                           | Desligue a atualização automática de tempo de integração auto-hospedada.       | Não       |
| **Conta SwitchService** "`<domain\user>`"`<password>`"]           | Desplique o DIAHostService para funcionar como uma nova conta. Utilize a palavra-passe vazia "" para contas do sistema e contas virtuais. | Não       |


## <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados

Quando se movimentam dados entre as instalações e a nuvem, a atividade utiliza um tempo de integração auto-hospedado para transferir os dados entre uma fonte de dados no local e a nuvem.

Aqui está um resumo de alto nível dos passos de fluxo de dados para copiar com um IR auto-hospedado:

![A visão geral de alto nível do fluxo de dados](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Um desenvolvedor de dados cria um tempo de integração auto-hospedado dentro de uma fábrica de dados Azure usando um cmdlet PowerShell. Atualmente, o portal Azure não suporta esta funcionalidade.
1. O desenvolvedor de dados cria um serviço ligado para uma loja de dados no local. O desenvolvedor fá-lo especificando a instância de tempo de funcionamento da integração auto-hospedada que o serviço deve usar para se ligar a lojas de dados.
1. O nó de tempo de integração auto-hospedado encripta as credenciais utilizando a Interface de Programação de Aplicações de Proteção de Dados do Windows (DPAPI) e guarda as credenciais localmente. Se vários nós estiverem definidos para alta disponibilidade, as credenciais são sincronizadas em outros nós. Cada nó encripta as credenciais usando dPAPI e armazena-as localmente. A sincronização credencial é transparente para o desenvolvedor de dados e é tratada pelo IR auto-hospedado.
1. A Azure Data Factory comunica com o tempo de execução de integração auto-hospedado para agendar e gerir empregos. A comunicação é através de um canal de controlo que utiliza uma ligação de [retransmissão](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) de ônibus de serviço Azure partilhada. Quando um trabalho de atividade precisa de ser executado, data Factory faz fila do pedido juntamente com qualquer informação credencial. Fá-lo caso as credenciais já não estejam armazenadas no tempo de funcionação da integração auto-hospedada. O tempo de integração auto-organizado começa o trabalho depois de ele sondagem da fila.
1. O tempo de execução de integração auto-hospedado copia dados entre uma loja no local e armazenamento em nuvem. A direção da cópia depende da configuração da atividade da cópia no pipeline de dados. Para este passo, o tempo de integração auto-hospedado comunica diretamente com serviços de armazenamento baseados na nuvem, como o armazenamento Azure Blob através de um canal HTTPS seguro.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Considerações para usar um IR auto-hospedado

- Você pode usar um único tempo de integração auto-hospedado para várias fontes de dados no local. Também pode partilhá-lo com outra fábrica de dados dentro do mesmo inquilino azure Ative Directory (Azure AD). Para mais informações, consulte [Partilhar um tempo de execução de integração auto-hospedado.](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory)
- Só pode instalar uma instância de um tempo de execução de integração auto-hospedado em qualquer máquina. Se tiver duas fábricas de dados que precisam de aceder às fontes de dados no local, utilize a funcionalidade de partilha de [INFRAVERMELHOS auto-hospedada](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) para partilhar o IR auto-hospedado, ou instale o IR auto-hospedado em dois computadores no local, um para cada fábrica de dados.  
- O tempo de funcionação de integração auto-hospedado não precisa de estar na mesma máquina que a fonte de dados. No entanto, ter o tempo de execução de integração auto-hospedado perto da fonte de dados reduz o tempo de execução da integração auto-hospedada para se ligar à fonte de dados. Recomendamos que instale o tempo de execução de integração auto-hospedado numa máquina que difere da que acolhe a fonte de dados no local. Quando o tempo de funcionação de integração auto-hospedado e a fonte de dados estão em máquinas diferentes, o tempo de funcionação de integração auto-hospedado não compete com a fonte de dados para recursos.
- Pode ter vários tempos de execução de integração auto-hospedados em diferentes máquinas que se ligam à mesma fonte de dados no local. Por exemplo, se tiver dois tempos de integração auto-hospedados que servem duas fábricas de dados, a mesma fonte de dados no local pode ser registada em ambas as fábricas de dados.
- Se já tiver um portal instalado no seu computador para servir um cenário power BI, instale um tempo de integração auto-hospedado separado para data factory noutra máquina.
- Utilize um tempo de execução de integração auto-hospedado para apoiar a integração de dados dentro de uma rede virtual Azure.
- Trate a sua fonte de dados como uma fonte de dados no local que está por trás de uma firewall, mesmo quando utiliza o Azure ExpressRoute. Utilize o tempo de execução de integração auto-hospedado para ligar o serviço à fonte de dados.
- Utilize o tempo de execução de integração auto-hospedado mesmo que a loja de dados esteja na nuvem de uma infraestrutura Azure como uma máquina virtual de Serviço (IaaS).
- As tarefas podem falhar num tempo de integração auto-hospedado que instalou num servidor Windows para o qual a encriptação compatível com FIPS está ativada. Para resolver este problema, desative a encriptação compatível com fips no servidor. Para desativar a encriptação compatível com fips, altere o valor da subchave de registo seguinte de 1 (ativada) para 0 (desativada): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Pré-requisitos

- As versões suportadas do Windows são:
  + Windows 7 Service Pack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   A instalação do tempo de execução da integração auto-hospedada num controlador de domínio não é suportada.
- .NET Quadro 4.6.1 ou posterior é necessário. Se estiver a instalar o tempo de execução de integração auto-hospedado numa máquina do Windows 7, instale .NET Framework 4.6.1 ou mais tarde. Consulte os requisitos do [sistema de enquadramento .NET](/dotnet/framework/get-started/system-requirements) para obter detalhes.
- A configuração mínima recomendada para a máquina de tempo de execução de integração auto-hospedada é um processador de 2 GHz com quatro núcleos, 8 GB de RAM e 80 GB de espaço de disco rígido disponível.
- Se a máquina hospedeira hibernar, o tempo de funcionamento da integração auto-hospedado não responde aos pedidos de dados. Configure um plano de alimentação adequado no computador antes de instalar o tempo de execução de integração auto-hospedado. Se a máquina estiver configurada para hibernar, o instalador de tempo de integração auto-hospedado solicita com uma mensagem.
- Deve ser um administrador na máquina para instalar e configurar com sucesso o tempo de execução de integração auto-hospedado.
- As execuções de copy-activity acontecem com uma frequência específica. O uso do processador e da RAM na máquina segue o mesmo padrão com tempos de pico e ocioso. O uso de recursos também depende fortemente da quantidade de dados que são movidos. Quando vários trabalhos de cópia estão em andamento, você vê o uso de recursos subir durante os tempos de pico.
- As tarefas podem falhar durante a extração de dados em formatos Parquet, ORC ou Avro. Para mais informações sobre o Parquet, consulte o [formato Parquet na Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime) A criação de ficheiros funciona na máquina de integração auto-hospedada. Para funcionar como esperado, a criação de ficheiros requer os seguintes pré-requisitos:
    - [Visual C++ 2010 Redistribuável](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Pacote (x64)
    - Java Runtime (JRE) versão 8 de um fornecedor JRE como [Adopt OpenJDK](https://adoptopenjdk.net/). Certifique-se de que a variável ambiente `JAVA_HOME` está definida.

## <a name="installation-best-practices"></a>Instalação de boas práticas

Pode instalar o tempo de execução da integração auto-hospedado, descarregando um pacote de configuração MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Consulte o artigo [Mova os dados entre as instalações e a nuvem](tutorial-hybrid-copy-powershell.md) para obter instruções passo a passo.

- Configure um plano de energia na máquina hospedeira para o tempo de funcionação de integração auto-hospedado para que a máquina não hiberna. Se a máquina hospedeira hibernar, o tempo de funcionação de integração auto-hospedado fica offline.
- Back up regularmente as credenciais associadas ao tempo de execução de integração auto-hospedado.
- Para automatizar as operações de configuração de INFRAVERMELHOS auto-hospedadas, consulte a [Configuração de um IR auto-hospedado existente via PowerShell](#setting-up-a-self-hosted-integration-runtime).  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Instale e registe um IR auto-hospedado no Microsoft Download Center

1. Vá à página de [descarregamento](https://www.microsoft.com/download/details.aspx?id=39717)do tempo de integração da Microsoft .
1. **Selecione Baixar,** selecione a versão de 64 bits e selecione **Next**. A versão de 32 bits não é suportada.
1. Execute o ficheiro MSI diretamente, ou guarde-o para o seu disco rígido e execute-o.
1. Na janela **Welcome,** selecione um idioma e selecione **Next**.
1. Aceite os Termos da Licença de Software da Microsoft e selecione **Next**.
1. Selecione **pasta** para instalar o tempo de execução de integração auto-hospedado e selecione **Next**.
1. Na página **Ready to install,** selecione **Instalar**.
1. Selecione **Terminar** para concluir a instalação.
1. Obtenha a chave de autenticação utilizando o PowerShell. Aqui está um exemplo da PowerShell para recuperar a chave de autenticação:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. Na janela de tempo de funcionamento da **integração do registo (auto-hospedado)** do Microsoft Integration Runtime Configuration Manager em execução na sua máquina, tome os seguintes passos:

    1. Colar a chave de autenticação na área de texto.

    1. Opcionalmente, selecione **a chave de autenticação do Show** para ver o texto chave.

    1. Selecione **Registar**.

## <a name="high-availability-and-scalability"></a>Elevada disponibilidade e escalabilidade

Pode associar um tempo de execução de integração auto-hospedado a múltiplas máquinas no local ou máquinas virtuais em Azure. Estas máquinas são chamadas de nó. Você pode ter até quatro nódosos associados a um tempo de execução de integração auto-hospedado. Os benefícios de ter vários nódosos em máquinas no local que têm uma porta de entrada instalada para uma porta de entrada lógica são:

* Maior disponibilidade do tempo de funcionamento da integração auto-hospedado para que não seja mais o único ponto de falha na sua solução de big data ou integração de dados em nuvem com data Factory. Esta disponibilidade ajuda a garantir a continuidade quando utiliza até quatro nós.
* Melhor desempenho e produção durante o movimento de dados entre as lojas de dados no local e na nuvem. Obtenha mais informações sobre [comparações](copy-activity-performance.md)de desempenho.

Pode associar vários nódosos instalando o software de tempo de execução de integração auto-hospedado a partir do [Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Em seguida, registe-a utilizando qualquer uma das teclas de autenticação obtidas a partir do **New-AzDataFactoryV2IntegrationRuntimeKey** cmdlet, conforme descrito no [tutorial](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Não é necessário criar um novo tempo de integração auto-hospedado para associar cada nó. Pode instalar o tempo de funcionação de integração auto-hospedado noutra máquina e registá-lo utilizando a mesma chave de autenticação.

> [!NOTE]
> Antes de adicionar outro nó para alta disponibilidade e escalabilidade, certifique-se de que o **acesso remoto à opção intranet** está ativado no primeiro nó. Para isso, selecione **Microsoft Integration Runtime Configuration Manager** > **Definições** > **acesso remoto à intranet**.

### <a name="scale-considerations"></a>Considerações de escala

#### <a name="scale-out"></a>Aumentar horizontalmente

Quando o uso do processador for elevado e a memória disponível for baixa no IR auto-hospedado, adicione um novo nó para ajudar a escalar a carga através das máquinas. Se as atividades falharem porque têm tempo para fora ou o nó de IR auto-hospedado está offline, ajuda se adicionar um nó ao portal.

#### <a name="scale-up"></a>Aumentar verticalmente

Quando o processador e a RAM disponível não são bem utilizados, mas a execução de empregos simultâneos atinge os limites de um nó, aumentando o número de empregos simultâneos que um nó pode executar. Você também pode querer aumentar quando as atividades tempo de intervalo porque o IR auto-hospedado está sobrecarregado. Como mostra a seguinte imagem, pode aumentar a capacidade máxima para um nó:  

![Aumentar o número de empregos simultâneos que podem funcionar num nó](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL

Aqui estão os requisitos para o certificado TLS/SSL que utiliza para garantir a comunicação entre os nós de tempo de execução da integração:

- O certificado deve ser um certificado X509 v3 de confiança pública. Recomendamos que utilize certificados emitidos por uma autoridade de certificação de parceiros públicos (CA).
- Cada nó de tempo de integração deve confiar neste certificado.
- Não recomendamos certificados de Nome Alternativo Sujeito (SAN) porque apenas o último item SAN é usado. Todos os outros itens da SAN são ignorados. Por exemplo, se tiver um certificado SAN cujos SANs são **node1.domain.contoso.com** e **node2.domain.contoso.com,** só pode utilizar este certificado numa máquina cujo nome de domínio totalmente qualificado (FQDN) seja **node2.domain.contoso.com**.
- O certificado pode utilizar qualquer tamanho chave suportado pelo Windows Server 2012 R2 para certificados SSL.
- Os certificados que usam chaves CNG não são suportados.  

> [!NOTE]
> Este certificado é utilizado:
>
> - Para encriptar portas num nó de INFRAVERMELHOS auto-hospedado.
> - Para a comunicação nó-ao-nó para sincronização do Estado, que inclui credenciais sincronização de serviços ligados em nós.
> - Quando um cmdlet PowerShell é utilizado para configurações credenciais de serviço ligados a partir de uma rede local.
>
> Sugerimos que utilize este certificado se o seu ambiente de rede privada não for seguro ou se pretender garantir a comunicação entre nós dentro da sua rede privada.
>
> O movimento de dados em trânsito de um IR auto-hospedado para outras lojas de dados acontece sempre dentro de um canal encriptado, independentemente de este certificado estar ou não definido.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Criar um tempo de integração auto-hospedado partilhado na Azure Data Factory

Você pode reutilizar uma infraestrutura de tempo de integração auto-hospedada existente que você já estabeleceu em uma fábrica de dados. Esta reutilização permite criar um tempo de integração auto-hospedado ligado numa fábrica de dados diferente, referindo-se a um IR auto-hospedado partilhado existente.

Para ver uma introdução e demonstração desta funcionalidade, veja o seguinte vídeo de 12 minutos:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **IR partilhado**: Um IR original auto-hospedado que funciona numa infraestrutura física.  
- **Linked IR**: Um IR que faz referência a outro IR partilhado. O IR ligado é um IR lógico e utiliza a infraestrutura de outro IR auto-hospedado partilhado.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Métodos para partilhar um tempo de execução de integração auto-hospedado

Para partilhar um tempo de execução de integração auto-hospedado com várias fábricas de dados, consulte Create um tempo de execução de [integração auto-hospedado](create-shared-self-hosted-integration-runtime-powershell.md) partilhado para detalhes.

### <a name="monitoring"></a>Monitorização

#### <a name="shared-ir"></a>IR compartilhado

![Seleções para encontrar um tempo de execução de integração partilhada](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Monitorize um tempo de execução de integração partilhada](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>IR ligado

![Seleções para encontrar um tempo de execução de integração ligado](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Monitorizar um tempo de execução de integração ligado](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitações conhecidas da partilha de IR auto-hospedada

* A fábrica de dados em que é criado um IR ligado deve ter um [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Por padrão, as fábricas de dados criadas no portal Azure ou cmdlets PowerShell têm um MSI criado implicitamente. Mas quando uma fábrica de dados é criada através de um modelo de Gestor de Recursos Azure ou SDK, você deve definir a propriedade **identidade** explicitamente. Esta definição garante que o Gestor de Recursos cria uma fábrica de dados que contém um MSI.

* A Data Factory .NET SDK que suporta esta funcionalidade deve ser a versão 1.1.0 ou posterior.

* Para conceder permissão, precisa do papel de Proprietário ou do papel herdado do Proprietário na fábrica de dados onde existe o IR partilhado.

* A funcionalidade de partilha funciona apenas para fábricas de dados dentro do mesmo inquilino da AD Azure.

* Para [os utilizadores de](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)Anúncios Azure , a funcionalidade de pesquisa na UI, que lista todas as fábricas de dados utilizando uma palavra-chave de pesquisa, não [funciona.](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits) Mas enquanto o utilizador convidado for o proprietário da fábrica de dados, pode partilhar o IR sem a funcionalidade de pesquisa. Para o MSI da fábrica de dados que precisa de partilhar o IR, introduza esse MSI na caixa de autorização de **atribuição** e selecione **Adicionar** na UI da Fábrica de Dados.

  > [!NOTE]
  > Esta funcionalidade está disponível apenas na Data Factory V2.

## <a name="notification-area-icons-and-notifications"></a>Ícones e notificações da área de notificação

Se mover o cursor sobre o ícone ou mensagem na área de notificação, pode ver detalhes sobre o estado do tempo de execução da integração auto-hospedado.

![Notificações na área de notificação](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>Portos e firewalls

Há duas firewalls a considerar:

- A *firewall corporativa* que funciona no router central da organização
- A *firewall do Windows* que é configurada como um daemon na máquina local onde o tempo de execução de integração auto-hospedado é instalado

![As firewalls](media/create-self-hosted-integration-runtime/firewall.png)

Ao nível da firewall corporativa, é necessário configurar os seguintes domínios e portas de saída:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

Ao nível da firewall do Windows ou ao nível da máquina, estas portas de saída estão normalmente ativadas. Se não forem, pode configurar os domínios e portas numa máquina de tempo de execução de integração auto-hospedada.

> [!NOTE]
> Com base na sua fonte e pias, poderá ser necessário permitir domínios adicionais e portas de saída na sua firewall corporativa ou firewall Windows.
>
> Para algumas bases de dados em nuvem, como a Base de Dados Azure SQL e o Lago de Dados Azure, poderá ser necessário permitir endereços IP de máquinas de tempo de execução de integração auto-hospedadas na sua configuração de firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiar dados de uma fonte para um lavatório

Certifique-se de que permite corretamente as regras de firewall na firewall corporativa, a firewall do Windows da máquina de funcionança de integração auto-hospedada e a própria loja de dados. Permitir estas regras permite que o tempo de integração auto-hospedado se conectem com sucesso tanto à fonte como ao afundador. Ativar as regras para cada loja de dados que esteja envolvida na operação de cópia.

Por exemplo, para copiar de uma loja de dados no local para um lavatório de base de dados SQL ou um depósito de dados Azure SQL, tome as seguintes medidas:

1. Permita a comunicação tCP de saída na porta 1433 para a firewall do Windows e para a firewall corporativa.
1. Configure as definições de firewall da base de dados SQL para adicionar o endereço IP da máquina de tempo de execução de integração auto-hospedada à lista de endereços IP permitidos.

> [!NOTE]
> Se a sua firewall não permitir a porta de saída 1433, o tempo de funcionação de integração auto-hospedado não pode aceder diretamente à base de dados SQL. Neste caso, pode utilizar uma [cópia encenada](copy-activity-performance.md) para a Base de Dados SQL e o Armazém de Dados SQL. Neste cenário, é necessário apenas HTTPS (porta 443) para o movimento de dados.

## <a name="proxy-server-considerations"></a>Considerações de servidor proxy

Se o seu ambiente de rede corporativa utilizar um servidor proxy para aceder à internet, configure o tempo de execução de integração auto-hospedado para utilizar configurações de procuração apropriadas. Pode definir o proxy durante a fase inicial de registo.

![Especificar o representante](media/create-self-hosted-integration-runtime/specify-proxy.png)

Quando configurado, o tempo de execução de integração auto-hospedado utiliza o servidor proxy para se ligar à fonte e destino do serviço na nuvem (que utilizam o protocolo HTTP ou HTTPS). É por isso que seleciona o **link Change** durante a configuração inicial.

![Definir o proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Existem três opções de configuração:

- **Não utilize proxy**: O tempo de funcionamento da integração auto-hospedado não utiliza explicitamente qualquer procuração para se ligar aos serviços de nuvem.
- **Utilização proxy do sistema**: O tempo de funcionação de integração auto-hospedado utiliza a definição de procuração configurada em diahost.exe.config e diawp.exe.config. Se estes ficheiros não especificarem nenhuma configuração de procuração, o tempo de execução de integração auto-hospedado liga-se diretamente ao serviço de nuvem sem passar por um proxy.
- **Utilize proxy personalizado**: Configure a definição de proxy HTTP para utilizar para o tempo de execução de integração auto-hospedado, em vez de utilizar configurações em diahost.exe.config e diawp.exe.config. **São** necessários valores de endereço e **porta.** **Os valores** de nome de utilizador e **palavra-passe** são opcionais, dependendo da definição de autenticação do seu representante. Todas as definições são encriptadas com o Windows DPAPI no tempo de execução de integração auto-hospedado e armazenadas localmente na máquina.

O serviço de anfitriões de tempo de integração reinicia automaticamente depois de guardar as definições de procuração atualizadas.

Depois de registar o tempo de execução de integração auto-hospedado, se pretender visualizar ou atualizar as definições de proxy, utilize o Microsoft Integration Runtime Configuration Manager.

1. Abra **o Gestor de Configuração**de Tempo de Execução da Integração da Microsoft .
1. Selecione o separador **Definições.**
1. Em **http proxy,** selecione o link **Alterar** para abrir a caixa de diálogo set **HTTP Proxy.**
1. Selecione **Seguinte**. Em seguida, consulte um aviso que pede a sua permissão para salvar a definição de procuração e reiniciar o serviço de hospedar tempo de integração.

Pode utilizar a ferramenta de gestor de configuração para visualizar e atualizar o proxy HTTP.

![Ver e atualizar o proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Se configurar um servidor proxy com autenticação NTLM, o serviço de anfitriões de tempo de integração passa por baixo da conta de domínio. Se alterar mais tarde a palavra-passe para a conta de domínio, lembre-se de atualizar as definições de configuração do serviço e reiniciar o serviço. Devido a este requisito, sugerimos que aceda ao servidor proxy utilizando uma conta de domínio dedicada que não o exija atualizar a palavra-passe com frequência.

### <a name="configure-proxy-server-settings"></a>Configurar as definições do servidor proxy

Se selecionar a opção **de procuração** do sistema Use para o proxy HTTP, o tempo de funcionação de integração auto-hospedado utiliza as definições de procuração em diahost.exe.config e diawp.exe.config. Quando estes ficheiros não especificam qualquer procuração, o tempo de execução de integração auto-hospedado liga-se diretamente ao serviço de nuvem sem passar por um proxy. O seguinte procedimento fornece instruções para a atualização do ficheiro diahost.exe.config:

1. No File Explorer, faça uma cópia segura do C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config como uma cópia de segurança do ficheiro original.
1. Abra o bloco de notas como administrador.
1. No Bloco de Notas, abra o ficheiro de texto C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config.
1. Encontre a etiqueta **de system.net** padrão, conforme mostrado no seguinte código:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Pode então adicionar detalhes do servidor proxy, como mostrado no seguinte exemplo:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    A etiqueta proxy permite que propriedades adicionais especifiquem as definições necessárias, como `scriptLocation`. Consulte [\<proxy\> Element (Definições de Rede)](https://msdn.microsoft.com/library/sa91de1e.aspx) para sintaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. Guarde o ficheiro de configuração na sua localização original. Em seguida, reinicie o serviço de hospedar o tempo de integração auto-hospedado, que capta as alterações.

   Para reiniciar o serviço, utilize os serviços applet do Painel de Controlo. Ou do Gestor de Configuração do Tempo de Execução de Integração, selecione o botão **Stop Service** e, em seguida, selecione **Start Service**.

   Se o serviço não arrancar, provavelmente adicionou sintaxe de etiqueta Sintaxe XML incorreta no ficheiro de configuração da aplicação que editou.

> [!IMPORTANT]
> Não se esqueça de atualizar tanto o diahost.exe.config e o diawp.exe.config.

Também precisa de se certificar de que o Microsoft Azure está na lista de autorizações da sua empresa. Pode descarregar a lista de endereços IP do Azure válidos do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Possíveis sintomas para problemas relacionados com o firewall e servidor proxy

Se vir mensagens de erro como as seguintes, a razão provável é a configuração inadequada da firewall ou do servidor proxy. Tal configuração impede que o tempo de funcionamento da integração auto-hospedado se ligue à Data Factory para se autenticar. Para garantir que o seu servidor de firewall e proxy está corretamente configurado, consulte a secção anterior.

* Quando tenta registar o tempo de execução de integração auto-hospedado, recebe a seguinte mensagem de erro: "Falhou em registar este nó de Tempo de Execução de Integração! Confirme que a chave de Autenticação é válida e que o serviço de hospedamento do serviço de integração está a funcionar nesta máquina."
* Ao abrir o Gestor de Configuração do Tempo de Integração, vê um estado de **desconectado** ou **de ligação**. Quando visualiza os registos de eventos do Windows, em registos de > **de visualização de** **eventos** > Prazo de Integração da **Microsoft,** vê mensagens de erro como esta:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>Permitir o acesso remoto a partir de uma intranet

Se utilizar o PowerShell para encriptar credenciais de uma máquina em rede que não seja onde instalou o tempo de execução de integração auto-hospedado, pode ativar o Acesso Remoto a partir da opção **Intranet.** Se executar o PowerShell para encriptar credenciais na máquina onde instalou o tempo de execução de integração auto-hospedado, não pode ativar o **Acesso Remoto a partir da Intranet**.

Ative **o Acesso Remoto a partir da Intranet** antes de adicionar outro nó para alta disponibilidade e escalabilidade.  

Quando executa a versão de configuração de tempo de integração auto-hospedada 3.3 ou posterior, por padrão, o instalador de tempo de integração auto-hospedado desativa o **Acesso Remoto da Intranet** na máquina de tempo de execução de integração auto-hospedada.

Quando utilizar uma firewall de um parceiro ou de outros, pode abrir manualmente a porta 8060 ou a porta configurada pelo utilizador. Se tiver um problema de firewall ao configurar o tempo de execução de integração auto-hospedado, utilize o seguinte comando para instalar o tempo de execução de integração auto-hospedado sem configurar a firewall:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Se optar por não abrir a porta 8060 na máquina de tempo de execução de integração auto-hospedada, utilize mecanismos que não sejam a aplicação Definição de Credenciais de Definição para configurar as credenciais de loja de dados. Por exemplo, pode utilizar o **cmdlet PowerShell PowerShell de PowerShell.**

## <a name="next-steps"></a>Passos seguintes

Para instruções passo a passo, consulte [Tutorial: Copie os dados no local para cloud](tutorial-hybrid-copy-powershell.md).
