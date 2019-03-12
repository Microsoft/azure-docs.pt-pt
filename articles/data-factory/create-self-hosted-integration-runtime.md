---
title: Criar um runtime de integração autoalojado no Azure Data Factory | Documentos da Microsoft
description: Saiba como criar um runtime de integração autoalojado no Azure Data Factory, que permite que as fábricas de dados acessar arquivos de dados de uma rede privada.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: d1e43924634e3c4543c244043cb06d965a4d14e0
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576842"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Criar e configurar um runtime de integração autoalojado
O integration runtime (IR) é a infraestrutura de computação do Azure Data Factory utiliza para fornecer capacidades de integração de dados em diferentes ambientes de rede. Para obter detalhes sobre o runtime de integração, consulte [descrição geral do runtime de integração](concepts-integration-runtime.md).

Um runtime de integração autoalojado pode executar atividades de cópia entre um arquivo de dados na cloud e um arquivo de dados numa rede privada e, podem expedir as atividades de transformação relativamente aos recursos de computação numa rede no local ou uma rede virtual do Azure. A instalação de um runtime de integração autoalojado precisa numa máquina no local ou uma máquina virtual (VM) dentro de uma rede privada.  

Este documento descreve como pode criar e configurar um ir autoalojado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Passos de alto nível para instalar um runtime de integração autoalojado
1. Criar um integration runtime autoalojado. Pode utilizar a IU do Azure Data Factory para esta tarefa. Eis um exemplo do PowerShell:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Transferir](https://www.microsoft.com/download/details.aspx?id=39717) e instalar o runtime de integração autoalojado num computador local.

3. Obter a chave de autenticação e registar o runtime de integração autoalojado com a chave. Eis um exemplo do PowerShell:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template-automation"></a>Configurar um runtime de integração autoalojado numa VM do Azure com um modelo Azure Resource Manager (automatização)
Pode automatizar a configuração do Runtime de integração autoalojado numa máquina virtual do Azure, utilizando [este modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Este modelo fornece uma forma fácil de ter um funcionamento totalmente autoalojado dentro de uma rede virtual do Azure com funcionalidades de elevada disponibilidade e escalabilidade (desde que definir a contagem de nós para 2 ou superior).

## <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados
Quando mover dados entre locais e a nuvem, a atividade utiliza um runtime de integração autoalojado para transferir os dados a partir de uma origem de dados no local para a cloud e vice-versa.

Este é um fluxo de dados de alto nível para o resumo de passos para copiar com um ir Autoalojado:

![Descrição geral de alto nível](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. O desenvolvedor de dados cria um runtime de integração autoalojado dentro de uma fábrica de dados do Azure com um cmdlet do PowerShell. Atualmente, o portal do Azure não suporta esta funcionalidade.
2. O desenvolvedor de dados cria um serviço ligado para um arquivo de dados no local, especificando a instância do runtime de integração autoalojado que ele deve utilizar para ligar aos arquivos de dados. Como parte da configuração de serviço ligado, o desenvolvedor de dados utiliza a aplicação do Gestor de credenciais (atualmente não suportada) para a definição de tipos de autenticação e credenciais. A aplicação do Gestor de credenciais se comunica com o arquivo de dados para testar a ligação e o runtime de integração autoalojado para guardar as credenciais.
3. O nó do runtime de integração autoalojado encripta as credenciais com o Windows dados DPAPI Protection Application Programming Interface () e guarda as credenciais localmente. Se vários nós forem definidos para elevada disponibilidade, as credenciais são ainda mais sincronizadas em todos os outros nós. Cada nó encripta as credenciais utilizando DPAPI e armazena-os localmente. Sincronização de credenciais é transparente para o desenvolvedor de dados e é manipulada pelo ir autoalojado.    
4. O serviço Data Factory se comunica com o runtime de integração autoalojado para agendamento e gestão de tarefas por meio de um *canal de controlo* que utiliza uma fila compartilhada do Azure Service Bus. Quando uma tarefa de atividade tem de ser executado, Data Factory coloca em fila o pedido, juntamente com informações de credenciais (caso as credenciais já não estão armazenadas no runtime de integração autoalojado). O runtime de integração autoalojado inicia a tarefa depois da fila de consulta.
5. O runtime de integração autoalojado copia dados de um arquivo no local para um armazenamento na cloud, ou vice versa dependendo da configuração a atividade de cópia no pipeline de dados. Para este passo, o runtime de integração autoalojado se comunica diretamente com os serviços de armazenamento baseado na nuvem, como o armazenamento de Blobs do Azure através de um canal de seguro (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Considerações sobre a utilização de um ir Autoalojado

- Um runtime de integração autoalojado único pode ser utilizado para várias origens de dados no local. Um runtime de integração autoalojado único pode ser partilhado com outro fábrica de dados no mesmo inquilino do Azure Active Directory. Para obter mais informações, consulte [partilha um runtime de integração autoalojado](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories).
- Pode ter apenas uma instância de um runtime de integração autoalojado instalado numa única máquina. Se tiver duas fábricas de dados que precisem de aceder a origens no local dados, tem de instalar o runtime de integração autoalojado em dois computadores no local. Em outras palavras, um runtime de integração autoalojado está associado a uma fábrica de dados específico.
- O runtime de integração autoalojado não precisa de estar no mesmo computador, como a origem de dados. No entanto, o mais perto da origem de dados com o runtime de integração autoalojado reduz o tempo para o runtime de integração autoalojado ligar à origem de dados. Recomendamos que instale o runtime de integração autoalojado num computador que é diferente dessa origem de dados de locais de anfitriões. Quando a origem de dados e do runtime de integração autoalojado em computadores diferentes, o runtime de integração autoalojado não compitam por recursos com a origem de dados.
- Pode ter vários runtimes de integração autoalojado em diferentes computadores que se ligam à mesma origem de dados no local. Por exemplo, pode ter dois runtimes de integração autoalojado que sirva duas fábricas de dados, mas a mesma origem de dados no local é registrada em ambas as fábricas de dados.
- Se já tiver um gateway instalado no seu computador para ser usado um cenário do Power BI, instale um runtime de integração autoalojado separada do Azure Data Factory em outra máquina.
- O runtime de integração autoalojado tem de ser utilizado para dar suporte a integração de dados dentro de uma rede virtual do Azure.
- Trate a sua origem de dados como uma origem de dados no local que está protegido por uma firewall, mesmo quando usa o Azure ExpressRoute. Utilize o runtime de integração autoalojado para estabelecer a conectividade entre o serviço e a origem de dados.
- Tem de utilizar o runtime de integração autoalojado, mesmo se o arquivo de dados na cloud numa máquina virtual IaaS do Azure.
- Tarefas poderão falhar num runtime de integração autoalojado que é instalado num servidor Windows na qual compatíveis com FIPS encriptação está ativada. Para contornar este problema, desative a encriptação compatível com FIPS no servidor. Para desativar a encriptação compatível com FIPS, altere o valor de registo seguinte de 1 (ativado) para 0 (desativado): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Pré-requisitos

- As versões de sistema operativo suportado são o Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Não é suportada a instalação do runtime de integração autoalojado num controlador de domínio.
- .NET framework 4.6.1 ou posterior é necessária. Se estiver a instalar o runtime de integração autoalojado num computador Windows 7, instalar o .NET Framework 4.6.1 ou posterior. Ver [requisitos de sistema do .NET Framework](/dotnet/framework/get-started/system-requirements) para obter detalhes.
- A configuração recomendada para a máquina de runtime de integração autoalojado é, pelo menos, 2 GHz, quatro núcleos, 8 GB de RAM e um disco de 80GB.
- Se a máquina de anfitrião hiberna, o runtime de integração autoalojado não responde a pedidos de dados. Configure uma esquema de energia adequado no computador antes de instalar o runtime de integração autoalojado. Se a máquina está configurada em hibernação, a instalação do runtime de integração autoalojado pede-lhe uma mensagem.
- Tem de ser um administrador no computador para instalar e configurar o runtime de integração autoalojado com êxito.
- Execuções de atividade de cópia ser efetuadas numa frequência específica. Utilização de recursos (CPU, memória) no computador segue o mesmo padrão com horas de pico e tempos de inatividade. Utilização de recursos também depende muito a quantidade de dados a ser movidos. Quando várias tarefas de cópia em curso, verá subir durante as horas de pico de utilização de recursos.

## <a name="installation-best-practices"></a>Melhores práticas de instalação
Pode instalar o runtime de integração autoalojado ao transferir um pacote de instalação do MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Ver [mover dados entre locais e na cloud artigo](tutorial-hybrid-copy-powershell.md) para obter instruções passo a passo.

- Configure um esquema de energia no computador anfitrião para o runtime de integração autoalojado para que a máquina sem a hibernação. Se a máquina de anfitrião hiberna, o runtime de integração autoalojado fica offline.
- Criar cópias de segurança as credenciais associadas com o runtime de integração autoalojado regularmente.

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Instalar e registar o runtime de integração autoalojado do Centro de Download

1. Vá para o [página de download do Microsoft integration runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Selecione **baixe**, selecione a versão de 64 bits (32 bits não é suportado) e selecione **próxima**.
3. Execute o ficheiro MSI diretamente, ou guardá-lo no seu disco rígido e executá-lo.
4. Sobre o **bem-vindo** página, selecione um idioma e selecione **próxima**.
5. Aceite os termos de licença de Software da Microsoft e selecione **seguinte**.
6. Selecione **pasta** para instalar o runtime de integração autoalojado e selecione **próxima**.
7. Sobre o **pronto para instalar o** página, selecione **instalar**.
8. Clique em **concluir** para concluir a instalação.
9. Obtenha a chave de autenticação com o Azure PowerShell. Eis um exemplo do PowerShell para obter a chave de autenticação:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Sobre o **registar o Integration Runtime (autoalojado)** página do Microsoft Integration Runtime Configuration Manager em execução no seu computador, siga os passos seguintes:

    a. Cole a chave de autenticação na área de texto.

    b. Opcionalmente, selecione **chave de autenticação Show** para ver o texto da chave.

    c. Selecione **Registar**.


## <a name="high-availability-and-scalability"></a>Elevada disponibilidade e escalabilidade
Um runtime de integração autoalojado pode ser associado a várias máquinas no local. Estas máquinas são chamadas de nós. Pode ter até quatro nós associados a um runtime de integração autoalojado. Os benefícios de ter vários nós (máquinas no local com um gateway instalado) para um gateway lógico são:
* Disponibilidade mais elevada do runtime de integração autoalojado, de modo que ele 's já não é o ponto único de falha na sua integração de dados de grandes volumes de dados solução ou na cloud, Azure Data Factory, garantindo a continuidade com até quatro nós.
* Melhorou o desempenho e a produtividade durante o movimento de dados entre aplicações no local e na cloud arquivos de dados. Obtenha mais informações sobre [comparações de desempenho](copy-activity-performance.md).

Pode associar vários nós ao instalar o software de runtime de integração autoalojado do [Centro de transferências](https://www.microsoft.com/download/details.aspx?id=39717). Em seguida, registre-se com uma das chaves de autenticação obtido do **New-AzDataFactoryV2IntegrationRuntimeKey** cmdlet, conforme descrito no [tutorial](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Não precisa de criar o novo runtime de integração autoalojado para associar a cada nó. Pode instalar o runtime de integração autoalojado em outra máquina e registá-lo com a mesma chave de autenticação. 

> [!NOTE]
> Antes de adicionar outro nó para elevada disponibilidade e escalabilidade, certifique-se de que o **acesso remoto à intranet** opção está ativada no primeiro nó (**Gestor de configuração do Microsoft Integration Runtime**  >  **Configurações** > **acesso remoto à intranet**). 

### <a name="scale-considerations"></a>Considerações de dimensionamento

#### <a name="scale-out"></a>Aumentar horizontalmente

Quando a memória disponível no runtime de integração autoalojado é baixa e a utilização da CPU é alta, adicionar um novo nó ajuda a aumentar horizontalmente a carga entre máquinas. Se atividades estão a falhar porque eles estão exceder o tempo limite ou porque o nó do Runtime de integração autoalojado está offline, isso será útil se adicionar um nó para o gateway.

#### <a name="scale-up"></a>Aumentar verticalmente

Quando a memória disponível e a CPU não são utilizados bem, mas a execução de tarefas simultâneas está a atingir o limite, deve aumentar verticalmente ao aumentar o número de tarefas simultâneas que podem ser executadas num nó. Também poderá querer aumentar verticalmente quando as atividades são exceder o tempo limite porque o runtime de integração autoalojado está sobrecarregado. Conforme mostrado na imagem seguinte, pode aumentar a capacidade máxima para um nó:  

![Cada vez maiores tarefas simultâneas que podem ser executadas num nó](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL

Eis os requisitos para o certificado TLS/SSL que é utilizado para proteger as comunicações entre o integration runtime nós:

- O certificado tem de ser um publicamente fidedigno X509 v3 certificado. Recomendamos que utilize certificados emitidos por um público (partner) autoridade de certificação (AC).
- Cada nó do integration runtime têm de confiar este certificado.
- Certificados de nome alternativo do requerente (SAN) não é recomendada porque será utilizado apenas o último item de SAN e todos os outros serão ignorados devido a limitações atuais. Por exemplo, se tiver um certificado SAN cujos SANs são **node1.domain.contoso.com** e **node2.domain.contoso.com**, pode utilizar este certificado apenas numa máquina cujo FQDN for  **node2.domain.contoso.com**.
- O certificado suporta qualquer tamanho de chave suportado pelo Windows Server 2012 R2 para certificados SSL.
- Certificados que utilizam chaves CNG não são suportados.  

> [!NOTE]
> Este certificado é utilizado para encriptar as portas no nó do Runtime de integração autoalojado, utilizado para **comunicação nó a nó** (para a sincronização de estado) e enquanto **com o PowerShell cmdlet para o serviço ligado de credenciais definição**de dentro da rede local. Sugerimos que utilize este certificado se o seu ambiente de rede privada não é seguro ou se quiser proteger a comunicação entre nós dentro da sua rede privada também. Movimento de dados em trânsito do Runtime de integração autoalojado para outros arquivos de dados sempre acontece através do canal encriptado, independentemente deste certificado ou não. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>Partilhar o runtime de integração autoalojado com várias fábricas de dados

É possível reutilizar uma infraestrutura de runtime de integração autoalojado existente que já configurou a numa fábrica de dados. Isto permite-lhe criar uma *ligado integration runtime autoalojado* numa diferentes dados fábrica fazendo referência um existente autoalojado (compartilhado).

Para partilhar um runtime de integração autoalojado com o PowerShell, consulte [criar um runtime de integração autoalojado partilhado na fábrica de dados do Azure com o PowerShell](create-shared-self-hosted-integration-runtime-powershell.md).

Para obter uma introdução de doze minutos e demonstração desta funcionalidade, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **Partilhado IR**: O IR autoalojado original que está em execução numa infraestrutura física.  
- **Runtime de integração de ligado**: O runtime de integração que faz referência a outro partilhado IR. Este é um runtime de integração lógico e utiliza a infraestrutura de outro Runtime de integração autoalojado (compartilhado).

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Passos de alto nível para criar um runtime de integração autoalojado ligado

1. No runtime de integração autoalojado para ser compartilhado, conceder permissão à fábrica de dados no qual pretende criar o ir ligado. 

   ![Botão para a concessão de permissão no separador Partilha](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Seleções para atribuição de permissões](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Tenha em atenção o ID de recurso do Runtime de integração autoalojado para ser partilhado.

   ![Localização do ID do recurso](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. Na fábrica de dados aos quais foram concedidas as permissões, criar um runtime de integração autoalojado novo (ligado) e introduza o ID de recurso.

   ![Botão para criar um runtime de integração autoalojado ligado](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Caixas de nome e ID de recurso](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Monitorização 

- **Runtime de integração partilhado**

  ![Seleções para localizar um runtime de integração partilhado](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Separador para monitorizar](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Runtime de integração ligado**

  ![Seleções para localizar um runtime de integração ligado](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Separador para monitorizar](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitações conhecidas de compartilhamento de Runtime de integração autoalojado

* A fábrica de dados no qual será criado um runtime de integração ligado tem de ter uma [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Por predefinição, as fábricas de dados criada no portal do Azure ou cmdlets do PowerShell tem um MSI criado implicitamente. Mas quando uma fábrica de dados é criada através de um modelo Azure Resource Manager ou o SDK, o **identidade** propriedade deve ser explicitamente definida para se certificar de que o Azure Resource Manager cria uma fábrica de dados que contém um MSI. 

* O SDK .NET do Azure Data Factory que suporte esta funcionalidade é a versão 1.1.0 ou posterior.

* Para conceder permissão, o utilizador tem a função de proprietário ou a função de proprietário herdada da fábrica de dados, onde existe o IR partilhado.

* Recurso de compartilhamento funciona apenas para as fábricas de dados no mesmo inquilino do Azure Active Directory.

* Para o Active Directory [utilizadores convidados](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews), a funcionalidade de pesquisa (listagem de todas as fábricas de dados utilizando uma palavra-chave de pesquisa) na IU [não funciona](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Mas, desde que o utilizador convidado é o proprietário da fábrica de dados, podem partilhar o runtime de integração sem a funcionalidade de pesquisa, ao escrever diretamente o MSI do data factory com a qual o runtime de integração precisa ser compartilhadas no **atribuir permissão** caixa de texto e selecionando **adicionar** na IU da fábrica de dados do Azure. 

  > [!NOTE]
  > Esta funcionalidade está disponível apenas no Azure Data Factory V2. 

## <a name="notification-area-icons-and-notifications"></a>Ícones da área de notificação e notificações

Se mover o cursor sobre o ícone ou a mensagem na área de notificação, pode encontrar detalhes sobre o estado do runtime de integração autoalojado.

![Notificações na área de notificação](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portas e firewall
Existem duas firewalls a serem considerados: o *firewall Corporativo* em execução no router central da organização e o *firewall do Windows* configurado como um daemon no computador local em que o runtime de integração autoalojado é instalado.

![Firewall](media/create-self-hosted-integration-runtime/firewall.png)

Com o *firewall Corporativo* nível, terá de configurar os seguintes domínios e as portas de saída:

Nomes de domínio | Portas | Descrição
------------ | ----- | ------------
*.servicebus.windows.net | 443 | Utilizado para comunicação com o serviço de movimento de dados back-end
*.core.windows.net | 443 | Utilizado para a cópia faseada através do armazenamento de Blobs do Azure (se configurada)
*.frontend.clouddatahub.net | 443 | Utilizado para comunicação com o serviço de movimento de dados back-end
download.microsoft.com | 443 | Utilizado para transferir as atualizações

Na *firewall do Windows* nível (nível de máquina), estas portas de saída normalmente estão ativadas. Se não, pode configurar as portas e domínios em conformidade numa máquina de runtime de integração autoalojado.

> [!NOTE]
> Com base na sua origem e sinks, poderá ter de domínios adicionais da lista de permissões e portas de saída na sua firewall empresarial ou a firewall do Windows.
>
> Para algumas bases de dados cloud (por exemplo, a base de dados do Azure SQL e o Azure Data Lake), poderá ter de permitir endereços IP de máquinas de runtime de integração autoalojado em suas configurações de firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiar dados de uma origem para um sink
Certifique-se de que as regras de firewall estão ativadas corretamente do firewall Corporativo, a firewall do Windows no computador de runtime de integração autoalojado, e o arquivo de dados em si. Ativar estas regras, permite que o runtime de integração autoalojado ligar a ambas as origem e sink com êxito. Ative as regras para cada arquivo de dados que está envolvido na operação de cópia.

Por exemplo, para copiar a partir de um arquivo de dados no local para um sink de base de dados do Azure SQL ou um coletor do Azure SQL Data Warehouse, siga os passos seguintes:

1. Permitir a comunicação de saída de TCP na porta 1433 para o firewall do Windows e o firewall Corporativo.
2. Configure as definições de firewall da base de dados SQL do Azure para adicionar o endereço IP do computador runtime de integração autoalojado para a lista de endereços IP permitidos.

> [!NOTE]
> Se a firewall não permite a saída através da porta 1433, o runtime de integração autoalojado não é possível acessar o banco de dados SQL do Azure diretamente. Neste caso, pode utilizar um [cópia faseada](copy-activity-performance.md) a SQL Database do Azure e Azure SQL Data Warehouse. Neste cenário, exigiria apenas HTTPS (porta 443) para o movimento de dados.


## <a name="proxy-server-considerations"></a>Considerações do servidor proxy
Se o seu ambiente de rede Corporativo utiliza um servidor proxy para aceder à internet, configure o runtime de integração autoalojado para utilizar definições de proxy apropriados. Pode definir o proxy durante a fase de registro inicial.

![Especificar o proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

O runtime de integração autoalojado utiliza o servidor proxy para ligar ao serviço cloud. Selecione **ligação de alteração** durante a configuração inicial. Verá a caixa de diálogo de definição de proxy.

![Proxy de conjunto](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Existem três opções de configuração:

- **Não utilizar o proxy**: O runtime de integração autoalojado não utiliza explicitamente qualquer proxy para ligar a serviços cloud.
- **Utilizar o proxy do sistema**: O runtime de integração autoalojado utiliza a definição de proxy que está configurado no diahost.exe.config e diawp.exe.config. Se nenhum proxy está configurado no diahost.exe.config e diawp.exe.config, o runtime de integração autoalojado liga-se ao serviço cloud diretamente sem passar por um proxy.
- **Utilizar proxy personalizado**: Configure a definição a utilizar para o runtime de integração autoalojado, em vez de usar configurações no diahost.exe.config e diawp.exe.config de proxy HTTP. **Endereço** e **porta** são necessários. **Nome de utilizador** e **palavra-passe** são opcionais, consoante a definição de autenticação de seu proxy. Todas as definições são encriptadas com a DPAPI do Windows, o tempo de execução de integração autoalojado e armazenadas localmente na máquina.

O serviço de anfitrião do integration runtime é reiniciado automaticamente depois de guardar as definições de proxy atualizadas.

Depois do integration runtime autoalojado for registado com êxito, se pretender ver ou atualizar as definições de proxy, utilize o Gestor de configuração do Runtime de integração.

1. Open **Gestor de configuração do Microsoft Integration Runtime**.
2. Mudar para o separador **Definições**.
3. Selecione o **alteração** ligação na **HTTP Proxy** secção para abrir o **definir o Proxy de HTTP** caixa de diálogo.
4. Selecione **Seguinte**. Em seguida, ver um aviso que pede sua permissão para guardar a definição de proxy e reinicie o serviço de anfitrião do integration runtime.

Pode ver e atualizar o proxy HTTP com a ferramenta Configuration Manager.

![Proxy de vista](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Se configurar um servidor proxy com a autenticação NTLM, o serviço de anfitrião do integration runtime é executado sob a conta de domínio. Se alterar a palavra-passe para a conta de domínio mais tarde, lembre-se atualizar as definições de configuração para o serviço e reinicie-o em conformidade. Devido a este requisito, sugerimos que utilize uma conta de domínio dedicado para aceder ao servidor de proxy que não necessita de atualizar a palavra-passe com frequência.

### <a name="configure-proxy-server-settings"></a>Configurar definições do servidor proxy

Se selecionar a **utilizar o proxy do sistema** definir para o proxy HTTP, o runtime de integração autoalojado utiliza a definição no diahost.exe.config e diawp.exe.config de proxy. Se não for especificado nenhum proxy no diahost.exe.config e diawp.exe.config, o runtime de integração autoalojado liga-se ao serviço cloud diretamente sem passar pelo proxy. O procedimento seguinte disponibiliza instruções para atualizar o ficheiro de diahost.exe.config:

1. No Explorador de ficheiros, faça uma cópia de segurança do C:\Program Files\Microsoft integração Runtime\3.0\Shared\diahost.exe.config para fazer backup do arquivo original.
2. Abra Notepad.exe executar como administrador e abra o ficheiro de texto C:\Program Files\Microsoft integração Runtime\3.0\Shared\diahost.exe.config. Encontre a etiqueta predefinida para system.net, conforme mostrado no seguinte código:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Em seguida, pode adicionar detalhes do servidor proxy, conforme mostrado no exemplo a seguir:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Propriedades adicionais são permitidas dentro da marca de proxy para especificar as definições necessárias, como `scriptLocation`. Ver [proxy elemento (definições de rede)](https://msdn.microsoft.com/library/sa91de1e.aspx) sintaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Guarde o ficheiro de configuração na localização original. Em seguida, reinicie o serviço de anfitrião, que seleciona as alterações de runtime de integração autoalojado. 

   Para reiniciar o serviço, utilize o miniaplicativo de serviços do painel de controlo. Ou selecione a partir do Gestor de configuração de Runtime de integração, o **parar serviço** e, em seguida, selecione **iniciar serviço**. 
   
   Se o serviço não iniciar, é provável que uma sintaxe de marca XML incorreta foi adicionada no ficheiro de configuração de aplicação que foi editado.

> [!IMPORTANT]
> Não se esqueça de atualizar diahost.exe.config e diawp.exe.config.

Terá também de certificar-se de que o Microsoft Azure está na lista de permissões da sua empresa. Pode baixar a lista de endereços IP do Microsoft Azure válidos do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Sintomas possíveis problemas relacionados a servidores de proxy e firewall
Se encontrar erros semelhantes aos seguintes, é provável devido a uma configuração incorreta do servidor proxy ou firewall, que bloqueia o runtime de integração autoalojado se a fábrica de dados para se autenticar. Para garantir que o seu servidor proxy e firewall estão configurados corretamente, consulte a secção anterior.

* Ao tentar registar o runtime de integração autoalojado, receberá o erro seguinte: "Falha ao registar este nó do Integration Runtime! Confirme que a chave de autenticação é válida e o serviço de integração de serviço de anfitrião está em execução nesta máquina."
* Quando abre o Gestor de configuração do Runtime de integração, ver o estado **desligado** ou **Connecting**. Quando estiver a ver registos de eventos do Windows, em **Visualizador de eventos** > **registos de serviços e aplicações** > **Microsoft Integration Runtime**, ver mensagens de erro como este:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Ativar o acesso remoto a partir de uma intranet  
Se utilizar o PowerShell ou a aplicação do Gestor de credenciais para encriptar as credenciais de outro computador (na rede) que não seja em que o runtime de integração autoalojado é instalado, pode ativar o **acesso remoto a partir da Intranet**opção. Se executar o PowerShell ou a aplicação do Gestor de credenciais para encriptar as credenciais no mesmo computador em que o runtime de integração autoalojado é instalado, não é possível ativar **acesso remoto a partir da Intranet**.

Deve habilitar **acesso remoto a partir da Intranet** antes de adicionar outro nó para elevada disponibilidade e escalabilidade.  

Durante a configuração do runtime de integração autoalojado (mais tarde 3.3.xxxx.x versão), por predefinição, a instalação do runtime de integração autoalojado desativa **acesso remoto a partir da Intranet** na máquina de runtime de integração autoalojado.

Se estiver a utilizar uma firewall de terceiros, é possível abrir manualmente a porta 8060 (ou a porta configurada pelo utilizador). Se tiver um problema de firewall ao configurar o runtime de integração autoalojado, experimente com o seguinte comando para instalar o runtime de integração autoalojado sem configurar a firewall:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> A aplicação do Gestor de credenciais ainda não está disponível para encriptar as credenciais no Azure Data Factory V2.  

Se optar por não abrir a porta 8060 na máquina de runtime de integração autoalojado, utilize mecanismos que não seja o aplicativo de credenciais de definição para configurar as credenciais do arquivo de dados. Por exemplo, pode utilizar o **New-AzDataFactoryV2LinkedServiceEncryptCredential** cmdlet do PowerShell.


## <a name="next-steps"></a>Passos Seguintes
Veja o tutorial seguinte para obter instruções passo a passo: [Tutorial: Copiar dados no local para a cloud](tutorial-hybrid-copy-powershell.md).
