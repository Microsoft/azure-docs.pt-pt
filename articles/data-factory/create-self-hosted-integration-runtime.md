---
title: Criar um tempo de execução de integração auto-hospedado no Azure Data Factory | Microsoft Docs
description: Saiba como criar um tempo de execução de integração auto-hospedado no Azure Data Factory, que permite que as fábricas de dados acessem armazenamentos de dados em uma rede privada.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/18/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 8ea6a365b0c7bc6c254c1313445bb54231e161ae
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285641"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Criar e configurar um tempo de execução de integração auto-hospedado
O IR (Integration Runtime) é a infraestrutura de computação que o Azure Data Factory usa para fornecer recursos de integração de dados em diferentes ambientes de rede. Para obter detalhes sobre o IR, consulte [visão geral do Integration Runtime](concepts-integration-runtime.md).

Um tempo de execução de integração auto-hospedado pode executar atividades de cópia entre um armazenamento de dados de nuvem e um armazenamento de dados em uma rede privada, e pode distribuir atividades de transformação em relação aos recursos de computação em uma rede local ou em uma rede virtual do Azure. A instalação de um Integration Runtime de hospedagem interna precisa em um computador local ou em uma VM (máquina virtual) dentro de uma rede privada.  

Este documento descreve como você pode criar e configurar um IR para hospedagem interna.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Etapas de alto nível para instalar um IR auto-hospedado
1. Criar um integration runtime autoalojado. Você pode usar a interface do usuário do Azure Data Factory para esta tarefa. Aqui está um exemplo do PowerShell:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Baixe](https://www.microsoft.com/download/details.aspx?id=39717) e instale o tempo de execução de integração auto-hospedado em um computador local.

3. Recupere a chave de autenticação e registre o tempo de execução de integração auto-hospedado com a chave. Aqui está um exemplo do PowerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template"></a>Configurando um IR auto-hospedado em uma VM do Azure usando um modelo de Azure Resource Manager 
Você pode automatizar a configuração de IR via hospedagem interna em uma máquina virtual do Azure usando [este modelo de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Esse modelo fornece uma maneira fácil de ter um IR totalmente hospedado internamente em uma rede virtual do Azure com recursos de alta disponibilidade e escalabilidade (desde que você defina a contagem de nós como 2 ou superior).

## <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados
Quando você move dados entre o local e a nuvem, a atividade usa um tempo de execução de integração auto-hospedado para transferir os dados de uma fonte de dados local para a nuvem e vice-versa.

Aqui está um fluxo de dados de alto nível para o resumo das etapas para copiar com um IR auto-hospedado:

![Descrição geral de alto nível](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. O desenvolvedor de dados cria um tempo de execução de integração auto-hospedado em um data factory do Azure usando um cmdlet do PowerShell. Atualmente, o portal do Azure não oferece suporte a esse recurso.
2. O desenvolvedor de dados cria um serviço vinculado para um armazenamento de dados local especificando a instância de tempo de execução de integração autohospedado que ele deve usar para se conectar a armazenamentos de dados.
3. O nó de tempo de execução de integração auto-hospedado criptografa as credenciais usando a interface de programação de aplicativo de proteção de dados do Windows (DPAPI) e salva as credenciais localmente. Se vários nós estiverem definidos para alta disponibilidade, as credenciais serão sincronizadas em outros nós. Cada nó criptografa as credenciais usando DPAPI e as armazena localmente. A sincronização de credenciais é transparente para o desenvolvedor de dados e é manipulada pelo IR auto-hospedado.    
4. O serviço Data Factory se comunica com o tempo de execução de integração auto-hospedado para agendamento e gerenciamento de trabalhos por meio de um *canal de controle* que usa uma [retransmissão do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay)compartilhada. Quando um trabalho de atividade precisa ser executado, o Data Factory enfileira a solicitação junto com qualquer informação de credencial (caso as credenciais ainda não estejam armazenadas no Integration Runtime de hospedagem interna). O tempo de execução de integração auto-hospedado inicia o trabalho depois de sondar a fila.
5. O Integration Runtime de hospedagem interna copia dados de um repositório local para um armazenamento em nuvem, ou vice-versa, dependendo de como a atividade de cópia está configurada no pipeline de dados. Para esta etapa, o Integration Runtime de hospedagem interna se comunica diretamente com serviços de armazenamento baseados em nuvem, como o armazenamento de BLOBs do Azure em um canal seguro (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Considerações sobre o uso de um IR auto-hospedado

- Um único tempo de execução de integração auto-hospedado pode ser usado para várias fontes de dados locais. Um único tempo de execução de integração auto-hospedado pode ser compartilhado com outro data factory dentro do mesmo locatário Azure Active Directory. Para obter mais informações, consulte [compartilhando um Integration Runtime de hospedagem interna](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories).
- Você pode ter apenas uma instância de um tempo de execução de integração auto-hospedado instalada em um único computador. Se você tiver duas fábricas de dados que precisam acessar fontes de dados locais, use o recurso de [compartilhamento de ir](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories) via hospedagem interna para compartilhar o tempo de execução de integração auto-hospedado ou instale o tempo de execução de integração auto-hospedado em dois computadores locais, um para cada data factory.  
- O tempo de execução de integração auto-hospedado não precisa estar no mesmo computador que a fonte de dados. No entanto, ter o tempo de execução de integração auto-hospedado mais próximo à fonte de dados reduz o tempo para o tempo de execução de integração auto-hospedado se conectar à fonte de dados. Recomendamos que você instale o tempo de execução de integração auto-hospedado em um computador diferente daquele que hospeda a fonte de dados local. Quando o tempo de execução de integração e a fonte de dados hospedados internamente estão em computadores diferentes, o tempo de execução de integração auto-hospedado não compete por recursos com a fonte de dados.
- Você pode ter vários tempos de execução de integração auto-hospedados em diferentes computadores que se conectam à mesma fonte de dados local. Por exemplo, você pode ter dois tempos de execução de integração auto-hospedados que atendem a duas data factories, mas a mesma fonte de dados local é registrada com ambas as fábricas de dados.
- Se você já tiver um gateway instalado em seu computador para atender a um cenário de Power BI, instale um tempo de execução de integração auto-hospedado separado para Azure Data Factory em outro computador.
- O Integration Runtime de hospedagem interna deve ser usado para dar suporte à integração de dados em uma rede virtual do Azure.
- Trate sua fonte de dados como uma fonte de dados local que está atrás de um firewall, mesmo quando você usa o Azure ExpressRoute. Use o tempo de execução de integração auto-hospedado para estabelecer a conectividade entre o serviço e a fonte de dados.
- Você deve usar o tempo de execução de integração auto-hospedado mesmo se o armazenamento de dados estiver na nuvem em uma máquina virtual IaaS do Azure.
- As tarefas podem falhar em um Integration Runtime de hospedagem interna que está instalado em um Windows Server no qual a criptografia compatível com FIPS está habilitada. Para contornar esse problema, desabilite a criptografia compatível com FIPS no servidor. Para desabilitar a criptografia compatível com FIPS, altere o seguinte valor de registro de 1 (habilitado) para 0 (desabilitado): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Pré-requisitos

- As versões de sistema operacional com suporte são o Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 e Windows Server 2019. Não há suporte para a instalação do tempo de execução de integração auto-hospedado em um controlador de domínio.
- .NET Framework 4.6.1 ou posterior é necessário. Se você estiver instalando o tempo de execução de integração auto-hospedado em um computador com Windows 7, instale .NET Framework 4.6.1 ou posterior. Consulte [.NET Framework requisitos do sistema](/dotnet/framework/get-started/system-requirements) para obter detalhes.
- A configuração recomendada para o computador do Integration Runtime de hospedagem interna é pelo menos 2 GHz, quatro núcleos, 8 GB de RAM e um disco de 80 GB.
- Se o computador host hibernar, o tempo de execução de integração auto-hospedado não responderá às solicitações de dados. Configure um plano de energia apropriado no computador antes de instalar o Integration Runtime de hospedagem interna. Se o computador estiver configurado para hibernar, a instalação do Integration Runtime de hospedagem interna solicitará uma mensagem.
- Você deve ser um administrador no computador para instalar e configurar o tempo de execução de integração auto-hospedado com êxito.
- Execuções de atividade de cópia ocorrem em uma frequência específica. O uso de recursos (CPU, memória) no computador segue o mesmo padrão com horários de pico e ocioso. A utilização de recursos também depende muito da quantidade de dados que estão sendo movidos. Quando vários trabalhos de cópia estiverem em andamento, você verá o uso do recurso subir durante horários de pico.
- As tarefas podem falhar ao extrair dados nos formatos parquet, ORC ou Avro. A criação do arquivo é executada no computador de integração auto-hospedado e requer que os pré-requisitos a seguir funcionem conforme o esperado (consulte o [formato parquet no Azure data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)).
    - [Pacote C++ redistribuível do Visual 2010](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) (x64)
    - Java Runtime (JRE) versão 8 de um provedor do JRE, como [adotar OpenJDK](https://adoptopenjdk.net/), garantindo que a variável de ambiente `JAVA_HOME` esteja definida.

## <a name="installation-best-practices"></a>Práticas recomendadas de instalação
Você pode instalar o tempo de execução de integração auto-hospedado baixando um pacote de instalação MSI do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Confira o [artigo mover dados entre o local e a nuvem](tutorial-hybrid-copy-powershell.md) para obter instruções passo a passo.

- Configure um plano de energia no computador host para o tempo de execução de integração auto-hospedado para que o computador não faça hibernação. Se o computador host hibernar, o tempo de execução de integração auto-hospedado ficará offline.
- Faça backup das credenciais associadas ao tempo de execução de integração auto-hospedado regularmente.
- Para automatizar as operações de configuração de IR de hospedagem interna, veja a [seção abaixo](#automation-support-for-self-hosted-ir-function).  

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Instalar e registrar o IR auto-hospedado no centro de download

1. Vá para a [página de download do Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Selecione **baixar**, selecione a versão de 64 bits (não há suporte para 32 bits) e selecione **Avançar**.
3. Execute o arquivo MSI diretamente ou salve-o no disco rígido e execute-o.
4. Na página de **boas-vindas** , selecione um idioma e selecione **Avançar**.
5. Aceite os termos de licença para software Microsoft e selecione **Avançar**.
6. Selecione a **pasta** para instalar o Integration Runtime auto-hospedado e selecione **Avançar**.
7. Na página **pronto para instalar** , selecione **instalar**.
8. Clique em **concluir** para concluir a instalação.
9. Obtenha a chave de autenticação usando Azure PowerShell. Aqui está um exemplo do PowerShell para recuperar a chave de autenticação:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Na página **registrar Integration Runtime (auto-hospedado)** do Microsoft Integration Runtime Configuration Manager em execução em seu computador, execute as seguintes etapas:

    a. Cole a chave de autenticação na área de texto.

    b. Opcionalmente, selecione **Mostrar chave de autenticação** para ver o texto da chave.

    c. Selecione **Registar**.

## <a name="automation-support-for-self-hosted-ir-function"></a>Suporte de automação para função IR auto-hospedada


> [!NOTE]
> Se você estiver planejando configurar o IR auto-hospedado em uma máquina virtual do Azure e desejar automatizar a instalação usando modelos de Azure Resource Manager, consulte a [seção](#setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template).

Você pode usar a linha de comando para configurar ou gerenciar um IR autônomo existente. Isso pode ser usado especialmente para automatizar a instalação, o registro de nós IR hospedados internamente. 

O **Dmgcmd. exe** está incluído na instalação hospedada internamente, normalmente localizada: C:\Arquivos de Programas\microsoft Integration Runtime\3.0\Shared\ pasta. Isso dá suporte a vários parâmetros e pode ser invocado via prompt de comando usando scripts do lote para automação. 

*Usos* 

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["password"] -Loglevel <logLevel> ] 
```

 *Detalhes (parâmetros/Propriedade):* 

| Propriedade                                                    | Descrição                                                  | Obrigatório |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| RegisterNewNode "`<AuthenticationKey>`"                     | Registrar Integration Runtime (auto-hospedado) nó com a chave de autenticação especificada | Não       |
| EnableRemoteAccess "`<port>`" ["`<thumbprint>`"]            | Habilitar o acesso remoto no nó atual para configurar um cluster de alta disponibilidade e/ou habilitar a configuração de credenciais diretamente em relação ao IR de hospedagem interna (sem passar pelo serviço ADF) usando  **Cmdlet New-AzDataFactoryV2LinkedServiceEncryptedCredential** de um computador remoto na mesma rede. | Não       |
| EnableRemoteAccessInContainer "`<port>`" ["`<thumbprint>`"] | Habilitar o acesso remoto ao nó atual quando o nó estiver em execução no contêiner | Não       |
| DisableRemoteAccess                                         | Desabilite o acesso remoto ao nó atual. O acesso remoto é necessário para a instalação de vários nós. O cmdlet New-**AzDataFactoryV2LinkedServiceEncryptedCredential** do PowerShell ainda funciona mesmo quando o acesso remoto é desabilitado, desde que seja executado no mesmo computador que o nó ir hospedado internamente. | Não       |
| Chave "`<AuthenticationKey>`"                                 | Substituir/atualizar a chave de autenticação anterior. Tenha cuidado, pois isso pode fazer com que o nó IR para hospedagem interna anterior fique offline, se a chave for de um tempo de execução de integração novo. | Não       |
| GenerateBackupFile "`<filePath>`" "`<password>`"            | Gerar arquivo de backup para o nó atual, o arquivo de backup inclui a chave do nó e as credenciais do repositório de dados | Não       |
| ImportBackupFile "`<filePath>`" "`<password>`"              | Restaurar o nó de um arquivo de backup                          | Não       |
| Reiniciar                                                     | Reiniciar o serviço de Host Integration Runtime (auto-hospedado)   | Não       |
| Iniciar                                                       | Iniciar o serviço de Host Integration Runtime (auto-hospedado)     | Não       |
| Parar                                                        | Parar o serviço de atualização de Integration Runtime (auto-hospedado)        | Não       |
| StartUpgradeService                                         | Iniciar o serviço de atualização de Integration Runtime (auto-hospedado)       | Não       |
| StopUpgradeService                                          | Parar o serviço de atualização de Integration Runtime (auto-hospedado)        | Não       |
| TurnOnAutoUpdate                                            | Ativar Integration Runtime (auto-hospedado) atualização automática        | Não       |
| TurnOffAutoUpdate                                           | Desligar Integration Runtime (auto-hospedado) atualização automática       | Não       |
| SwitchServiceAccount "< domínio \ usuário >" ["senha"]           | Defina DIAHostService para executar como uma nova conta. Usar senha vazia ("") para conta do sistema ou conta virtual | Não       |
| LogLevel `<logLevel>`                                       | Definir nível de log do ETW (desativado, erro, detalhado ou todos). Geralmente usado pelo suporte da Microsoft durante a depuração. | Não       |

   


## <a name="high-availability-and-scalability"></a>Alta disponibilidade e escalabilidade
Um tempo de execução de integração auto-hospedado pode ser associado a vários computadores locais ou máquinas virtuais no Azure. Esses computadores são chamados de nós. Você pode ter até quatro nós associados a um tempo de execução de integração auto-hospedado. Os benefícios de ter vários nós (computadores locais com um gateway instalado) para um gateway lógico são:
* Maior disponibilidade do tempo de execução de integração auto-hospedado para que ele não seja mais o ponto único de falha em sua solução de Big Data ou integração de dados de nuvem com Azure Data Factory, garantindo a continuidade com até quatro nós.
* Desempenho e taxa de transferência aprimorados durante a movimentação de dados entre armazenamentos de dados locais e na nuvem. Obtenha mais informações sobre [comparações de desempenho](copy-activity-performance.md).

Você pode associar vários nós instalando o software de tempo de execução de integração auto-hospedado do [centro de download](https://www.microsoft.com/download/details.aspx?id=39717). Em seguida, registre-o usando qualquer uma das chaves de autenticação obtidas do cmdlet **New-AzDataFactoryV2IntegrationRuntimeKey** , conforme descrito no [tutorial](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Você não precisa criar um novo tempo de execução de integração auto-hospedado para associar cada nó. Você pode instalar o tempo de execução de integração auto-hospedado em outro computador e registrá-lo usando a mesma chave de autenticação. 

> [!NOTE]
> Antes de adicionar outro nó para alta disponibilidade e escalabilidade, verifique se a opção **acesso remoto à intranet** está habilitada no primeiro nó (**Microsoft Integration Runtime Configuration Manager** **configurações** >   >  **Acesso remoto à intranet**). 

### <a name="scale-considerations"></a>Considerações sobre escala

#### <a name="scale-out"></a>Aumentar horizontalmente

Quando a memória disponível no IR auto-hospedado é baixa e o uso da CPU é alto, a adição de um novo nó ajuda a escalar horizontalmente a carga entre as máquinas. Se as atividades estiverem falhando porque estão atingindo o tempo limite ou porque o nó IR para hospedagem interna está offline, ele ajuda a adicionar um nó ao gateway.

#### <a name="scale-up"></a>Aumentar verticalmente

Quando a memória disponível e a CPU não são utilizadas bem, mas a execução de trabalhos simultâneos está atingindo o limite, você deve escalar verticalmente aumentando o número de trabalhos simultâneos que podem ser executados em um nó. Você também pode querer escalar verticalmente quando as atividades esgotam o tempo limite porque o IR hospedado automaticamente está sobrecarregado. Conforme mostrado na imagem a seguir, você pode aumentar a capacidade máxima de um nó:  

![Aumentando trabalhos simultâneos que podem ser executados em um nó](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL

Aqui estão os requisitos para o certificado TLS/SSL que é usado para proteger as comunicações entre nós do tempo de execução de integração:

- O certificado deve ser um certificado X509 v3 publicamente confiável. Recomendamos que você use certificados emitidos por uma autoridade de certificação (CA) pública (parceiro).
- Cada nó do Integration Runtime deve confiar nesse certificado.
- Não recomendamos certificados de SAN (nome alternativo da entidade) porque apenas o último item de SAN será usado e todos os outros serão ignorados devido a limitações atuais. Por exemplo, se você tiver um certificado SAN cujas SANs são **Node1.domain.contoso.com** e **NODE2.domain.contoso.com**, você poderá usar esse certificado somente em um computador cujo FQDN seja **NODE2.domain.contoso.com**.
- O certificado dá suporte a qualquer tamanho de chave com suporte do Windows Server 2012 R2 para certificados SSL.
- Não há suporte para certificados que usam chaves CNG.  

> [!NOTE]
> Esse certificado é usado para criptografar portas no nó IR auto-hospedado, usado para **comunicação de nó para nó** (para sincronização de estado que inclui sincronização de credenciais de serviços vinculados entre nós) e ao **usar o cmdlet do PowerShell para configuração de credencial de serviço vinculada** de dentro da rede local. Sugerimos o uso desse certificado se o seu ambiente de rede privada não for seguro ou se você quiser proteger a comunicação entre os nós em sua rede privada também. A movimentação de dados em trânsito do IR de infravermelho auto-hospedado para outros armazenamentos de dados sempre acontece usando o canal criptografado, independentemente desse conjunto de certificados ou não. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>Compartilhando o tempo de execução de integração auto-hospedado com várias fábricas de dados

Você pode reutilizar uma infraestrutura de tempo de execução de integração autohospedada existente que você já configurou em um data factory. Isso permite que você crie um *tempo de execução de integração autohospedado vinculado* em um data Factory diferente referenciando um ir (compartilhado) de hospedagem interna existente.

Para compartilhar um tempo de execução de integração auto-hospedado usando o PowerShell, consulte [criar um tempo de execução de integração hospedado internamente no Azure data Factory com o PowerShell](create-shared-self-hosted-integration-runtime-powershell.md).

Para uma introdução e uma demonstração de doze minutos desse recurso, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **Ir compartilhado**: o ir autohospedado original que está sendo executado em uma infraestrutura física.  
- **Ir vinculado**: o ir que faz referência a outro ir compartilhado. Esse é um IR lógico e usa a infraestrutura de outro IR hospedado automaticamente (compartilhado).

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Etapas de alto nível para a criação de um IR vinculado por hospedagem interna

1. No IR auto-hospedado a ser compartilhado, Conceda permissão para o data factory no qual você deseja criar o IR vinculado. 

   ![Botão para conceder permissão na guia compartilhamento](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Seleções para atribuir permissões](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Observe a ID de recurso do IR auto-hospedado a ser compartilhado.

   ![Local da ID do recurso](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. No data factory ao qual as permissões foram concedidas, crie um novo IR via hospedagem interna (vinculado) e insira a ID do recurso.

   ![Botão para criar um Integration Runtime vinculado de hospedagem interna](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Caixas para ID de nome e recurso](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Monitorização 

- **IR compartilhado**

  ![Seleções para localizar um Integration Runtime compartilhado](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Guia para monitoramento](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **IR vinculado**

  ![Seleções para localizar um tempo de execução de integração vinculado](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Guia para monitoramento](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitações conhecidas do compartilhamento de IR via hospedagem interna

* O data factory no qual um IR vinculado será criado deve ter um [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Por padrão, as fábricas de dados criadas nos cmdlets portal do Azure ou PowerShell têm um MSI criado implicitamente. Mas quando um data factory é criado por meio de um modelo de Azure Resource Manager ou SDK, a propriedade **Identity** deve ser definida explicitamente para garantir que Azure Resource Manager crie um data Factory que contenha um MSI. 

* O SDK do .NET Azure Data Factory que dá suporte a esse recurso é a versão 1.1.0 ou posterior.

* Para conceder permissão, o usuário precisa da função de proprietário ou da função de proprietário herdada na data factory em que existe o IR compartilhado.

* O recurso de compartilhamento funciona apenas para fábricas de dados dentro do mesmo locatário de Azure Active Directory.

* Para Active Directory [usuários convidados](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews), a funcionalidade de pesquisa (listando todas as fábricas de dados usando uma palavra-chave de pesquisa) na interface do usuário não [funciona](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Mas, desde que o usuário convidado seja o proprietário do data factory, ele pode compartilhar o IR sem a funcionalidade de pesquisa, digitando diretamente o MSI do data factory com o qual o IR precisa ser compartilhado na caixa de texto **atribuir permissão** e selecionando **Adicionar** na interface do usuário do Azure Data Factory. 

  > [!NOTE]
  > Este recurso está disponível somente no Azure Data Factory v2. 

## <a name="notification-area-icons-and-notifications"></a>Ícones e notificações da área de notificação

Se você mover o cursor sobre o ícone ou a mensagem na área de notificação, poderá encontrar detalhes sobre o estado do tempo de execução de integração auto-hospedado.

![Notificações na área de notificação](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portas e firewall
Há dois firewalls a serem considerados: o *firewall corporativo* em execução no roteador central da organização e o Firewall do *Windows* configurado como um daemon no computador local em que o tempo de execução de integração auto-hospedado está instalado.

![Firewall](media/create-self-hosted-integration-runtime/firewall.png)

No nível do *firewall corporativo* , você precisa configurar os seguintes domínios e portas de saída:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]


No nível do *Firewall do Windows* (nível do computador), essas portas de saída normalmente são habilitadas. Caso contrário, você pode configurar os domínios e as portas adequadamente em um computador do Integration Runtime de hospedagem interna.

> [!NOTE]
> Com base na fonte e nos coletores, talvez seja necessário permitir domínios adicionais e portas de saída no firewall corporativo ou no firewall do Windows.
>
> Para alguns bancos de dados de nuvem (por exemplo, banco de dados SQL do Azure e Azure Data Lake), talvez seja necessário permitir endereços IP de máquinas de tempo de execução de integração auto-hospedados em sua configuração de firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiar dados de uma origem para um coletor
Verifique se as regras de firewall estão habilitadas corretamente no firewall corporativo, no firewall do Windows na máquina de tempo de execução de integração auto-hospedado e no próprio repositório de dados. Habilitar essas regras permite que o Integration Runtime de hospedagem interna Conecte-se com êxito à origem e ao coletor. Habilite regras para cada armazenamento de dados envolvido na operação de cópia.

Por exemplo, para copiar de um armazenamento de dados local para um coletor de banco de dados SQL do Azure ou um coletor de SQL Data Warehouse do Azure, execute as seguintes etapas:

1. Permita a comunicação TCP de saída na porta 1433 para o Firewall do Windows e o firewall corporativo.
2. Defina as configurações de firewall do banco de dados SQL do Azure para adicionar o endereço IP do computador de tempo de execução de integração auto-hospedado à lista de endereços IP permitidos.

> [!NOTE]
> Se o firewall não permitir a porta de saída 1433, o Integration Runtime de hospedagem interna não poderá acessar o banco de dados SQL do Azure diretamente. Nesse caso, você pode usar uma [cópia em etapas](copy-activity-performance.md) para o banco de dados SQL do Azure e o Azure SQL data warehouse. Nesse cenário, você precisaria apenas de HTTPS (porta 443) para a movimentação de dados.


## <a name="proxy-server-considerations"></a>Considerações sobre o servidor proxy
Se o ambiente de rede corporativa usar um servidor proxy para acessar a Internet, configure o Integration Runtime para usar as configurações de proxy apropriadas. Você pode definir o proxy durante a fase de registro inicial.

![Especificar proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Quando configurado, o Integration Runtime de hospedagem interna usa o servidor proxy para se conectar ao serviço de nuvem, origem/destino (aqueles que usam o protocolo HTTP/HTTPS). Isso é selecionar **Alterar link** durante a configuração inicial. Você verá a caixa de diálogo configuração de proxy.

![Definir proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Há três opções de configuração:

- Não **usar proxy**: o Integration Runtime de hospedagem interna não usa explicitamente nenhum proxy para se conectar aos serviços de nuvem.
- **Usar o proxy do sistema**: o Integration Runtime de hospedagem interna usa a configuração de proxy configurada em diahost. exe. config e diawp. exe. config. Se nenhum proxy estiver configurado em diahost. exe. config e diawp. exe. config, o Integration Runtime de hospedagem interna se conectará diretamente ao serviço de nuvem sem passar por um proxy.
- **Usar proxy personalizado**: defina a configuração de proxy http a ser usada para o tempo de execução de integração auto-hospedado, em vez de usar as configurações em diahost. exe. config e diawp. exe. config. O **endereço** e a **porta** são necessários. O **nome de usuário** e a **senha** são opcionais, dependendo da configuração de autenticação do proxy. Todas as configurações são criptografadas com o Windows DPAPI no tempo de execução de integração auto-hospedado e armazenadas localmente no computador.

O serviço de host do Integration Runtime é reiniciado automaticamente depois que você salva as configurações de proxy atualizadas.

Depois que o tempo de execução de integração auto-hospedado tiver sido registrado com êxito, se você quiser exibir ou atualizar as configurações de proxy, use Integration Runtime Configuration Manager.

1. Abra **Microsoft Integration Runtime Configuration Manager**.
2. Mudar para o separador **Definições**.
3. Selecione o link **alterar** na seção **proxy http** para abrir a caixa de diálogo **Definir proxy http** .
4. Selecione **Seguinte**. Em seguida, você verá um aviso que solicita sua permissão para salvar a configuração de proxy e reiniciar o serviço de host do Integration Runtime.

Você pode exibir e atualizar o proxy HTTP usando a ferramenta Configuration Manager.

![Exibir proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Se você configurar um servidor proxy com autenticação NTLM, o serviço de host do Integration Runtime será executado na conta de domínio. Se você alterar a senha para a conta de domínio mais tarde, lembre-se de atualizar os parâmetros de configuração para o serviço e reiniciá-lo de acordo. Devido a esse requisito, sugerimos que você use uma conta de domínio dedicada para acessar o servidor proxy que não exige que você atualize a senha com frequência.

### <a name="configure-proxy-server-settings"></a>Definir configurações do servidor proxy

Se você selecionar a configuração **usar proxy do sistema** para o proxy http, o tempo de execução de integração auto-hospedado usará a configuração de proxy em diahost. exe. config e diawp. exe. config. Se nenhum proxy for especificado em diahost. exe. config e diawp. exe. config, o Integration Runtime de hospedagem interna se conectará diretamente ao serviço de nuvem sem passar pelo proxy. O procedimento a seguir fornece instruções para atualizar o arquivo diahost. exe. config:

1. No explorador de arquivos, faça uma cópia segura de C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config para fazer backup do arquivo original.
2. Abra o notepad. exe em execução como administrador e abra o arquivo de texto C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. Localize a marca padrão para system.net, conforme mostrado no código a seguir:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Em seguida, você pode adicionar detalhes do servidor proxy, conforme mostrado no exemplo a seguir:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Propriedades adicionais são permitidas dentro da marca de proxy para especificar as configurações necessárias, como `scriptLocation`. Consulte [elemento proxy (configurações de rede)](https://msdn.microsoft.com/library/sa91de1e.aspx) para obter a sintaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Salve o arquivo de configuração no local original. Em seguida, reinicie o serviço de host do Integration Runtime de hospedagem interna, que pega as alterações. 

   Para reiniciar o serviço, use o applet Serviços no painel de controle. Ou de Integration Runtime Configuration Manager, selecione o botão **parar serviço** e, em seguida, selecione **Iniciar serviço**. 
   
   Se o serviço não for iniciado, é provável que uma sintaxe de marca XML incorreta tenha sido adicionada no arquivo de configuração do aplicativo que foi editado.

> [!IMPORTANT]
> Não se esqueça de atualizar diahost. exe. config e diawp. exe. config.

Você também precisa ter certeza de que Microsoft Azure está na lista de permissões da sua empresa. Você pode baixar a lista de endereços IP de Microsoft Azure válidos no [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Possíveis sintomas para problemas relacionados ao firewall e ao servidor proxy
Se você encontrar erros semelhantes aos seguintes, é provável que seja devido à configuração incorreta do firewall ou do servidor proxy, o que impede que o tempo de execução de integração auto-hospedado se conecte ao Data Factory para se autenticar. Para garantir que o firewall e o servidor proxy estejam configurados corretamente, consulte a seção anterior.

* Ao tentar registrar o tempo de execução de integração auto-hospedado, você recebe o seguinte erro: "falha ao registrar este Integration Runtime nó! Confirme se a chave de autenticação é válida e se o serviço de host do serviço de integração está em execução neste computador. "
* Ao abrir Integration Runtime Configuration Manager, você verá um status de **desconectado** ou de **conexão**. Quando você estiver exibindo logs de eventos do Windows, em **Visualizador de Eventos** > **logs de aplicativos e serviços** > **Microsoft Integration Runtime**, você verá mensagens de erro como esta:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Habilitando o acesso remoto de uma intranet  
Se você usar o PowerShell para criptografar credenciais de outro computador (na rede) diferente de onde o tempo de execução de integração auto-hospedado está instalado, você poderá habilitar a opção **acesso remoto da intranet** . Se você executar o PowerShell para criptografar credenciais no mesmo computador em que o tempo de execução de integração auto-hospedado está instalado, não será possível habilitar o **acesso remoto da intranet**.

Você deve habilitar o **acesso remoto da intranet** antes de adicionar outro nó para alta disponibilidade e escalabilidade.  

Durante a instalação do tempo de execução de integração auto-hospedado (versão 3.3. xxxx. x posterior), por padrão, a instalação do tempo de execução de integração auto-hospedado desabilita o **acesso remoto da intranet** no computador do Integration Runtime de hospedagem interna.

Se você estiver usando um firewall de terceiros, poderá abrir manualmente a porta 8060 (ou a porta configurada pelo usuário). Se você tiver um problema de firewall durante a configuração do tempo de execução de integração auto-hospedado, tente usar o seguinte comando para instalar o tempo de execução de integração auto-hospedado sem configurar o firewall:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Se você optar por não abrir a porta 8060 no computador do Integration Runtime de hospedagem interna, use mecanismos diferentes do aplicativo de credenciais de configuração para configurar as credenciais do repositório de dados. Por exemplo, você pode usar o cmdlet do PowerShell **New-AzDataFactoryV2LinkedServiceEncryptCredential** .


## <a name="next-steps"></a>Passos seguintes
Consulte o tutorial a seguir para obter instruções passo a passo: [tutorial: copiar dados locais para a nuvem](tutorial-hybrid-copy-powershell.md).
