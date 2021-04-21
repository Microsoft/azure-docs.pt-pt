---
title: Usando Azure Migrar com pontos finais privados
description: Use o suporte de ligação privada Azure Migrate para descobrir, avaliar e migrar usando link privado.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/07/2020
ms.openlocfilehash: 82811c731c158d970d7ec2c2350a0cba106f6a67
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835493"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>Usando Azure Migrar com pontos finais privados  

Este artigo descreve como usar o Azure Migrate para descobrir, avaliar e migrar servidores através de uma rede privada utilizando [o link privado Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview). 

Pode utilizar o [Azure Migrate: Discovery and Assessment](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-discovery-and-assessment-tool) and [Azure Migrate: Server Migration](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) tools to connect private and securely to the Azure Migrate service over a ExpressRoute private peering or a site VPN connection, using Azure private and a 4URE link. 

O método de conectividade de ponto final privado é recomendado quando existe um requisito organizacional para aceder ao serviço Azure Migrate e outros recursos Azure sem atravessar redes públicas. Também pode utilizar o suporte de ligação privado para utilizar os circuitos de observação privados ExpressRoute existentes para melhores requisitos de largura de banda ou latência. 

## <a name="support-requirements"></a>Requisitos de apoio 

### <a name="required-permissions"></a>Permissões obrigatórias

**Permissões de Contribuinte + Administrador de Acesso ao Utilizador** ou **Proprietário** na subscrição. 

### <a name="supported-scenarios-and-tools"></a>Cenários e ferramentas apoiados

**Implementação** | **Detalhes** | **Ferramentas** 
--- | --- | ---
**Descoberta e Avaliação** | Realize uma descoberta e avaliação sem agente e em escala dos seus servidores em qualquer plataforma – plataformas de hipervisores como [VMware vSphere](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) ou [Microsoft Hyper-V,](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v)nuvens públicas como [AWS](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws) ou [GCP](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp), ou até [servidores de metal nus.](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical) | Azure Migrate: Descoberta e Avaliação  <br/> 
**Inventário de software** | Descubra aplicativos, papéis e funcionalidades em execução em VMware VMs. | Azure Migrate: Descoberta e Avaliação  
**Visualização da dependência** | Utilize a capacidade de análise de dependência para identificar e compreender dependências entre servidores. <br/> [A visualização de dependência de agentes](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies-agentless) é suportada de forma nativa com o suporte de ligação privada Azure Migrate. <br/>[A visualização de dependência baseada em agentes](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) requer conectividade à Internet. [aprender](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) a usar pontos finais privados para visualização de dependência baseada em agentes. | Azure Migrate: Descoberta e Avaliação |
**Migração** | Execute [migrações hiper-V sem agente](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) ou utilize a abordagem baseada em agente para migrar os seus [VMs VMware, VMs](./tutorial-migrate-vmware-agent.md) [hiper-V,](./tutorial-migrate-physical-virtual-machines.md) [servidores físicos,](./tutorial-migrate-physical-virtual-machines.md) [VMs em execução em AWS,](./tutorial-migrate-aws-virtual-machines.md) [VMs em execução em GCP,](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)ou VMs em execução em um fornecedor de virtualização diferente. | Azure Migrate: Migração do Servidor
 
>[!Note]
>
> [As migrações de VMware sem agente](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) requerem acesso à Internet ou conectividade através do espremomento da ExperessRoute Microsoft. <br/> [Aprenda](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute) a usar pontos finais privados para executar replicações sobre o espreitamento privado ExpressRoute ou uma ligação VPN site-to-site (S2S).  <br/><br/> 
   
#### <a name="other-integrated-tools"></a>Outras ferramentas integradas

Algumas ferramentas de migração podem não ser capazes de enviar dados de utilização para o projeto Azure Migrate se o acesso à rede pública for desativado. O projeto Azure Migrate deve ser configurado para permitir que o tráfego de todas as redes receba dados de outras ofertas da Microsoft ou de fornecedores de software independentes externos [(ISV).](https://docs.microsoft.com/azure/migrate/migrate-services-overview#isv-integration) 


Para permitir o acesso à rede pública para o projeto Azure Migrate, aceda à **página de propriedades** do Azure Migrate no portal Azure, selecione **Nº**, e selecione **Save**.

![Diagrama que mostra como alterar o modo de acesso à rede.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Outras considerações   

**Considerações** | **Detalhes**
--- | --- 
**Preços** | Para obter informações sobre preços, consulte [os preços da bolha Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) e os preços de [ligação privada Azure](https://azure.microsoft.com/pricing/details/private-link/).  
**Requisitos de rede virtual** | O ponto final do gateway ExpressRoute/VPN deve residir na rede virtual selecionada ou numa rede virtual ligada à rede. Pode necessitar de endereços IP ~15 na rede virtual.  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Criar um projeto com conectividade de ponto final privado

Use este [artigo](https://docs.microsoft.com/azure/migrate/create-manage-projects#create-a-project-for-the-first-time) para criar um novo projeto Azure Migrate. 

> [!Note]
> Não é possível alterar o método de conectividade para a conectividade de ponto final privado para os projetos existentes da Azure Migrate.

Na secção de configuração **Avançada,** forneça os detalhes abaixo para criar um ponto final privado para o seu projeto Azure Migrate.
- No **método conectividade,** escolha **ponto final privado.** 
- No **acesso ao ponto final do público desativar,** mantenha a definição predefinição  **Nº**. Algumas ferramentas de migração podem não ser capazes de enviar dados de utilização para o projeto Azure Migrate se o acesso à rede pública for desativado. [Saiba mais.](#other-integrated-tools)
- Na **subscrição de rede Virtual**, selecione a subscrição para a rede virtual de ponto final privado. 
- Na **rede Virtual,** selecione a rede virtual para o ponto final privado. O aparelho Azure Migrate e outros componentes de software que precisam de se ligar ao projeto Azure Migrate devem estar nesta rede ou numa rede virtual conectada.
- Na **Sub-rede,** selecione a sub-rede para o ponto final privado. 

Selecione **Criar**. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Não feche esta página enquanto a criação do projeto estiver em andamento.

![Criar o projeto](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

    
Isto cria um projeto migratório e anexa-lhe um ponto final privado. 

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Descubra e avalie servidores para migração usando link privado Azure 

### <a name="set-up-the-azure-migrate-appliance"></a>Configurar o aparelho Azure Migrate 

1. Em **Máquinas Discover**  >  **As suas máquinas estão virtualizadas?**
2. Na **tecla de projeto Generate Azure Migrate,** forneça um nome para o aparelho Azure Migrate. 
3. **Selecione A tecla Gerar** para criar os recursos Azure necessários. 

    > [!Important]
    > Não feche a página das máquinas Discover durante a criação de recursos.  
    - Neste passo, a Azure Migrate cria um cofre chave, conta de armazenamento, cofre dos Serviços de Recuperação (apenas para migrações de VMware sem agente), e alguns recursos internos e anexa um ponto final privado a cada recurso. Os pontos finais privados são criados na rede virtual selecionada durante a criação do projeto.  
    - Uma vez criados os pontos finais privados, os registos de recursos DNS CNAME para os recursos Azure Migrate são atualizados para um pseudónimo num subdomínio com o prefixo 'privatelink'. Por padrão, a Azure Migrate também cria uma zona privada de DNS correspondente à subdomínio 'privatelink' para cada tipo de recurso e insere registos DNS A para os pontos finais privados associados. Isto permite que o aparelho Azure Migrate e outros componentes de software que residem na rede de origem cheguem aos pontos finais de recursos do Azure Migrate em endereços IP privados.  
    - A Azure Migrate também permite uma [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para o projeto migrador, e concede permissões à identidade gerida para aceder de forma segura à conta de armazenamento.  

4. Depois de a chave ser gerada com sucesso, copie os detalhes chave para configurar e registar o aparelho.   

#### <a name="download-the-appliance-installer-file"></a>Descarregue o ficheiro do instalador do aparelho  

Azure Migrate: Descoberta e avaliação utilize um aparelho Azure Migrate leve. O aparelho executa a descoberta do servidor e envia metadados de configuração e desempenho do servidor para a Azure Migrate.

Para configurar o aparelho, descarregue o ficheiro com fecho de correr que contém o script do instalador a partir do portal. Copie o ficheiro com fecho no servidor que irá hospedar o aparelho. Depois de descarregar o ficheiro com fecho, verifique a segurança do ficheiro e execute o script do instalador para implantar o aparelho. 

Aqui estão os links de descarregamento de cada um dos cenários com os seus valores de haxixe:

Scenario | Ligação para transferência | Valor de hash
--- | --- | ---
Hyper-V | [AzureMigrateInstaller-HyperV-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160557) | 17EFA01E3A7683F1CE2A08E3A9197A27D8BD2CC03C3AB5C6E00E4261A822BDB3
Físico | [AzureMigrateInstaller-Physical-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160558) | 01028F92C2095452F2DDCB89986CDC1F17AAC58E150A50A5B219A69CF1B7DA3BE0
VMware | [AzureMigrateInstaller-VMware-public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160648) | 66D3217AEC1DE51D84EC608B22BDDA605EC9C4FBAB06FC69FEC9858627C224
Escala VMware | [AzureMigrateInstaller-VMware-Public-Scaleout-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160811) | 42C1E8D5CF428E35E5E5E4E465DD08439F0FD5C319340CE3E3ADC3DC1717A6

#### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. Abra uma janela de comando do administrador no servidor para a qual descarregou o ficheiro.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped

    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Uso de exemplo para nuvem pública: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink.zip SHA256 ```

3.  Verifique a versão mais recente do aparelho comparando os valores de haxixe da tabela acima.

Certifique-se de que o servidor satisfaz os requisitos de [hardware](https://docs.microsoft.com/azure/migrate/migrate-appliance) para o cenário escolhido (VMware/Hyper-V/Físico ou outro) e pode ligar-se aos URLs Azure necessários - nuvens públicas e [governamentais.](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) [](./migrate-appliance.md#government-cloud-urls-for-private-link-connectivity)


#### <a name="run-the-script"></a>Executar o script

1. Extraia o ficheiro com fecho para uma pasta no servidor que irá hospedar o aparelho. 
2. Lançar PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do ficheiro fechado descarregado.
4. Executar o script **AzureMigrateInstaller.ps1,** da seguinte forma:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink> .\AzureMigrateInstaller.ps1```
   
5. Depois de o script ser executado com sucesso, ele lança o gestor de configuração do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Configure o aparelho e inicie a descoberta contínua

Abra um browser em qualquer máquina que possa ligar ao servidor do aparelho e abra o URL do gestor de configuração do aparelho: `https://appliance name or IP address: 44368` . Em alternativa, pode abrir o gestor de configuração a partir do ambiente de trabalho do servidor do aparelho selecionando o atalho para o gestor de configuração.

#### <a name="set-up-prerequisites"></a>Configurar pré-requisitos

1. Leia as informações de terceiros e aceite os termos da **licença.**    
 
2. No gestor de configuração > **Configurar pré-requisitos,** faça o seguinte:
   - **Conectividade**: O aparelho verifica o acesso aos URLs necessários. Se o servidor utilizar um representante:
     - Selecione **Configurar o representante** para especificar o endereço de procuração `http://ProxyIPAddress` ou a porta de `http://ProxyFQDN` escuta.
     - Especifique as credenciais se o proxy precisar de autenticação. Apenas é suportado o proxy HTTP.
     - Se quiser, pode adicionar uma lista de endereços URLs/IP que devem contornar o servidor proxy. Se estiver a utilizar o espreitamento privado ExpressRoute, certifique-se de que contorna estes [URLs](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute#configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations).
     - Tem de selecionar **Guardar** para registar a configuração se tiver atualizado os dados do servidor proxy ou adicionados endereços URLs/IP para contornar o proxy.
     
        > [!Note]
        > Se estiver a obter um erro com aka.ms/* de ligação durante a verificação de conectividade e não quiser que o aparelho aceda a este URL através da internet, tem de desativar o serviço de atualização automática do aparelho seguindo os passos [**aqui**](https://docs.microsoft.com/azure/migrate/migrate-appliance#turn-off-auto-update). Após a desativação automática da atualização, a verificação de conectividade URL aka.ms/* será ignorada. 

   - **Sincronização de tempo**: O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta funcione corretamente.
   - **Instalação de atualizações**: O aparelho assegura a instalação das atualizações mais recentes. Depois de concluída a verificação, pode selecionar **Os serviços do aparelho** para ver o estado e as versões dos serviços em execução no servidor do aparelho.
        > [!Note]
        > Se optou por desativar o serviço de atualização automática do aparelho, pode atualizar manualmente os serviços do aparelho para obter as versões mais recentes dos serviços seguindo os passos [**aqui**](https://docs.microsoft.com/azure/migrate/migrate-appliance#manually-update-an-older-version).
   - **Instale vDDK**:_(Necessário apenas para aparelho VMware)_ O aparelho verifica se o Kit de Desenvolvimento de Discos Virtuais VMware vSphere (VDDK) está instalado. Se não estiver instalado, faça o download do VDDK 6.7 da VMware e extraia o conteúdo zip descarregado para o local especificado no aparelho, conforme indicado nas **instruções de instalação**.

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Registe o aparelho e inicie a descoberta contínua

Após a verificação dos pré-requisitos, siga estes passos para registar o aparelho e inicie a descoberta contínua para os respetivos cenários: [VMware VMs,](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#register-the-appliance-with-azure-migrate) [Hiper-VMs,](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#register-the-appliance-with-azure-migrate) [Servidores Físicos,](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical#register-the-appliance-with-azure-migrate) [VMs AWS,](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws#register-the-appliance-with-azure-migrate) [VMs GCP](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp#register-the-appliance-with-azure-migrate).


>[!Note]
> Se estiver a obter problemas de resolução de DNS durante o registo do aparelho ou no momento da descoberta inicial, certifique-se de que os recursos da Azure Migrate criados durante o portal **'Gerar'** são alcançáveis a partir do servidor no local que hospeda o aparelho Azure Migrate. [Saiba mais sobre como verificar a conectividade da rede.](#troubleshoot-network-connectivity)

### <a name="assess-your-servers-for-migration-to-azure"></a>Avalie os seus servidores para a migração para Azure
Após a descoberta estar concluída, avalie os seus servidores[(VMware VMs,](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm) [Hiper-VMs,](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v) [servidores físicos,](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm) [VMs AWS,](https://docs.microsoft.com/azure/migrate/tutorial-assess-aws) [VMs GCP](https://docs.microsoft.com/azure/migrate/tutorial-assess-gcp)) para migração para VMs Azure ou Solução VMware Azure (AVS), utilizando a ferramenta Azure Migrate: Discovery and Assessment. 

Também pode [avaliar as suas máquinas no local](https://docs.microsoft.com/azure/migrate/tutorial-discover-import#prepare-the-csv) com a ferramenta Azure Migrate: Discovery and Assessment utilizando um ficheiro de valores separados por vírgula importada (CSV).   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>Migrar servidores para Azure usando link privado Azure

As secções seguintes descrevem os passos necessários para utilizar a Azure Migrate com [pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para migrações utilizando ligações expressRoute private peering ou VPN.  

Este artigo mostra uma via de implementação de prova de conceito para replicações baseadas em agentes para migrar os seus [VMs VMs, VMs](./tutorial-migrate-vmware-agent.md) [hiper-V,](./tutorial-migrate-physical-virtual-machines.md) [servidores físicos,](./tutorial-migrate-physical-virtual-machines.md) [VMs em execução em AWS,](./tutorial-migrate-aws-virtual-machines.md) [VMs em execução em GCP,](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)ou VMs em execução em um fornecedor de virtualização diferente usando pontos finais privados Azure. Você pode usar uma abordagem semelhante para realizar [migrações hiper-V sem agente](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) usando link privado.

>[!Note]
>[As migrações de VMware sem agente](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical) requerem acesso à Internet ou conectividade através do espremomento da ExperessRoute Microsoft. 

### <a name="set-up-a-replication-appliance-for-migration"></a>Criar um aparelho de replicação para migração 

O diagrama que se segue ilustra o fluxo de trabalho de replicação baseado em agente com pontos finais privados utilizando a ferramenta Azure Migrate: Server Migration.  

![Arquitetura de replicação](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

A ferramenta utiliza um aparelho de replicação para replicar os seus servidores para o Azure. Utilize este artigo para [preparar e configurar uma máquina para o aparelho de replicação. ](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#prepare-a-machine-for-the-replication-appliance)

Depois de configurar o aparelho de replicação, utilize as seguintes instruções para criar os recursos necessários para a migração. 

1. In **Discover machines**  >  **Are your machines virtualized?** 
2. Na **região Alvo,** selecione e confirme a região de Azure para a qual pretende migrar as máquinas.
3. Selecione **Criar recursos** para criar os recursos Azure necessários. Não feche a página durante a criação de recursos.   
    - Isto cria um cofre dos Serviços de Recuperação em segundo plano e permite uma identidade gerida para o cofre. Um cofre de Serviços de Recuperação é uma entidade que contém a informação de replicação dos servidores e é usada para desencadear operações de replicação.  
    - Se o projeto Azure Migrate tiver conectividade de ponto final privado, é criado um ponto final privado para o cofre dos Serviços de Recuperação. Isto adiciona cinco nomes privados totalmente qualificados (FQDNs) ao ponto final privado, um por cada microserviço ligado ao cofre dos Serviços de Recuperação.   
    - Os cinco nomes de domínio são formatados neste padrão: <br/> _{Vault-ID}-asr-pod01-{type}-. {target-geo-code}_. privatelink.siterecovery.windowsazure.com  
    - Por padrão, a Azure Migrate cria automaticamente uma zona privada de DNS e adiciona registos DNS A para os microserviços de cofre dos Serviços de Recuperação. A zona privada de DNS está então ligada à rede virtual de ponto final privado. Isto permite que o aparelho de replicação no local resolva os nomes de domínio totalmente qualificados nos seus endereços IP privados.

4. Antes de registar o aparelho de replicação, certifique-se de que os FQDNs de ligação privada do cofre são alcançáveis a partir da máquina que hospeda o aparelho de replicação. [Saiba mais sobre como verificar a conectividade da rede.](#troubleshoot-network-connectivity) 

5. Assim que verificar a conectividade, descarregue a configuração do aparelho e o ficheiro da chave, verifique o processo de instalação e registe o aparelho para a Azure Migrate. Reveja os [passos detalhados aqui.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#set-up-the-replication-appliance) Depois de configurar o aparelho de replicação, siga estas instruções para [instalar o serviço de mobilidade](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#install-the-mobility-service) nas máquinas que pretende migrar. 

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Replicar servidores para Azure usando link privado Azure 

Agora, siga [estes passos](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) para selecionar servidores para replicação.  

Na   >  **replicação das definições de**  >  **Cache/Replication,** utilize a conta de armazenamento de drop-down para selecionar uma conta de armazenamento para replicar sobre um link privado.  

Se o seu projeto Azure Migrate tiver conectividade de ponto final privado, deve  [conceder permissões ao cofre dos Serviços de Recuperação gerido para](#grant-access-permissions-to-the-recovery-services-vault) aceder à conta de armazenamento exigida pela Azure Migrate.   

Além disso, para permitir replicações sobre uma ligação privada, [crie um ponto final privado para a conta de armazenamento.](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Conceder permissões de acesso ao cofre dos Serviços de Recuperação

A identidade gerida do cofre dos Serviços de Recuperação requer permissões para acesso autenticado à conta de armazenamento cache/replicação. 

Utilize as orientações abaixo para identificar o cofre dos Serviços de Recuperação criado pela Azure Migrate e conceda as permissões necessárias. 

**_Identifique o cofre dos serviços de recuperação e o ID do objeto de identidade gerido_**

Pode encontrar os detalhes do cofre dos Serviços de Recuperação na página Azure Migrate: Server Migration **Properties.** 

1. Vá ao **hub Azure Migrate**, selecione **Overview** on the Azure Migrate: Server Migration tile.

    ![Página geral no centro Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. No painel esquerdo, selecione **Propriedades**. Tome nota do nome do cofre dos Serviços de Recuperação e identificação de identidade gerida. O cofre terá _o ponto final privado_ como tipo de **conectividade** e _outros_ como o **tipo de replicação**. Vai precisar desta informação enquanto dá acesso ao cofre.
      
    ![Azure Migrate: Página de propriedades de migração de servidores](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_Conceder as permissões necessárias para aceder à conta de armazenamento_**

 A identidade gerida do cofre deve ser concedida as seguintes permissões de função na conta de armazenamento necessárias para a replicação.  Neste caso, deve criar a conta de armazenamento com antecedência.

>[!Note]
> Para migrar os VMs Hiper-V para Azure utilizando um link privado, deve conceder acesso à conta de armazenamento de replicação e à conta de armazenamento de cache. 

As permissões de função variam consoante o tipo de conta de armazenamento.

- Contas de armazenamento baseadas em gestores de recursos (tipo padrão):
  - [Contribuinte](../role-based-access-control/built-in-roles.md#contributor) _e_
  - [Contribuinte de Dados do Armazenamento de Blobs](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Contas de armazenamento baseadas em gestores de recursos (tipo Premium):
  - [Contribuinte](../role-based-access-control/built-in-roles.md#contributor) _e_
  - [Proprietário dos Dados do Armazenamento de Blobs](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Aceda à conta de armazenamento de replicação/cache selecionada para replicação. Selecione **o controlo de acesso (IAM)** no painel esquerdo. 

1. Na secção Adicionar uma tarefa **de função,** selecione **Adicionar**:

   ![Adicionar uma atribuição de função](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. Na página **de atribuição de funções Adicionar,** no campo **Role,** selecione a função adequada da lista de permissões acima mencionada. Introduza o nome do cofre anteriormente e selecione **Save**.

    ![Fornecer acesso baseado em funções](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. Além destas permissões, deve também permitir o acesso aos serviços fidedignos da Microsoft. Se o seu acesso à rede estiver restrito a redes **selecionadas,** selecione **Permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento** na secção Exceções no **separador Networking.** 

![Permitir serviços confiáveis da Microsoft para conta de armazenamento](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Criar um ponto final privado para a conta de armazenamento (opcional)

Para replicar usando o ExpressRoute com um estonte privado, [crie um ponto final privado](https://docs.microsoft.com/azure/private-link/tutorial-private-endpoint-storage-portal#create-storage-account-with-a-private-endpoint) para as contas de armazenamento de cache/replicação (subresource alvo: **_blob_**). 

>[!Note]
>
> - Só pode criar pontos finais privados numa conta de armazenamento De Finalidade Geral v2 (GPv2). Para obter informações sobre preços, consulte os preços das bolhas de página de [Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) e [os preços de link privado Azure](https://azure.microsoft.com/pricing/details/private-link/)

O ponto final privado da conta de armazenamento deve ser criado na mesma rede virtual que o projeto Azure Migrate, ou outra rede virtual ligada a esta rede. 

Selecione **Sim** e integre-o com uma zona privada de DNS. A zona privada de DNS ajuda a encaminhar as ligações da rede virtual para a conta de armazenamento através de um link privado. Selecionar **Sim** liga automaticamente a zona DNS à rede virtual e adiciona os registos DNS para a resolução de novos IPs e nomes de domínio totalmente qualificados criados. Saiba mais sobre [as zonas privadas do DNS.](https://docs.microsoft.com/azure/dns/private-dns-overview)

Se o utilizador que cria o ponto final privado for também o proprietário da conta de armazenamento, o ponto final privado será aprovado automaticamente. Caso contrário, o proprietário da conta de armazenamento deve aprovar o ponto final privado para utilização. Para aprovar ou rejeitar uma ligação de ponto final privado solicitada, aceda a **ligações de ponto final privados** em **Rede** na página da conta de armazenamento.

Reveja o estado do estado de ligação do ponto final privado antes de prosseguir. 

![Estado de aprovação do ponto de final privado](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Depois de ter criado o ponto final privado, utilize a conta de armazenamento cache de definições **de**  >  **replicação para**  >   selecionar a conta de armazenamento para replicar sobre um link privado.  

Certifique-se de que o aparelho de replicação no local tem conectividade de rede com a conta de armazenamento no seu ponto final privado. [Saiba mais sobre como verificar a conectividade da rede.](#troubleshoot-network-connectivity)

>[!Note]
>
> - Para as migrações de VM Hiper-V para Azure, se a conta de armazenamento de replicação for do tipo _Premium,_ deve selecionar outra conta de armazenamento do tipo _Standard_ para a conta de armazenamento de cache. Neste caso, deve criar pontos finais privados tanto para a replicação como para a conta de armazenamento de cache.  

Em seguida, siga estas instruções para [rever e iniciar a replicação](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) e realizar [migrações](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#run-a-test-migration).  

## <a name="troubleshoot-network-connectivity"></a>Resolver problemas de conectividade de rede 

### <a name="validate-private-endpoints-configuration"></a>Validar a configuração de pontos finais privados 

Certifique-se de que o ponto final privado é um estado aprovado.  

1. Vá para a página Azure Migrate: Discovery and Assessment and Server Migration properties.
2. A página de propriedades contém a lista de pontos finais privados e FQDNs de ligação privada que foram criadas automaticamente por Azure Migrate.  

3. Selecione o ponto final privado que pretende diagnosticar.  
    1. Validar que o estado de ligação está aprovado.
    2. Se a ligação estiver num estado pendente, tem de a aprovar.
    3. Também pode navegar para o recurso de ponto final privado e rever se a rede virtual corresponder à rede virtual do ponto final do projeto Migrate. 

    ![Ver ligação private endpoint](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)

### <a name="verify-dns-resolution"></a>Verificar resolução dns 

O aparelho no local (ou fornecedor de replicação) acederá aos recursos da Azure Migrate utilizando os seus nomes de domínio de ligação privada totalmente qualificados (FQDNs). Pode necessitar de configurações dns adicionais para resolver o endereço IP privado dos pontos finais privados a partir do ambiente de origem. [Utilize este artigo](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) para compreender os cenários de configuração do DNS que podem ajudar a resolver problemas de conectividade da rede.  

Para validar a ligação de ligação privada, efetue uma resolução DNS dos pontos finais de recursos Azure Migrate (recursos de ligação privada FQDNs) a partir do servidor de bordo que hospeda o aparelho Migrar e certifique-se de que se resolve para um endereço IP privado. Os detalhes do ponto final privado e a informação do recurso de ligação privada FQDNs está disponível nas páginas de propriedades de Discovery and Assessment e Server Migration. Selecione **Baixar as definições de DNS** para ver a lista.   

 ![Azure Migrate: Propriedades de Descoberta e Avaliação](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 ![Azure Migrate: Propriedades de migração de servidores](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties.png)

Um exemplo ilustrativo para a resolução de DNS da conta de armazenamento link privado FQDN.  

- _Insira nslookup<nome da conta de armazenamento>_.blob.core.windows.net.  Substitua <> de nome de conta de armazenamento pelo nome da conta de armazenamento utilizada para a Azure Migrate.  

    Receberá uma mensagem como esta:  

   ![Exemplo de resolução do DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- Um endereço IP privado de 10.1.0.5 é devolvido para a conta de armazenamento. Este endereço pertence à sub-rede de rede virtual de ponto final privado.   

Pode verificar a resolução do DNS para outros artefactos Azure Migrate utilizando uma abordagem semelhante.   

Se a resolução do DNS estiver incorreta, siga estes passos:  

- Se utilizar um DNS personalizado, reveja as definições de DNS personalizadas e valide que a configuração de DNS está correta. Para obter orientação, consulte [a visão geral do ponto final privado: configuração DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).
- Se utilizar servidores DNS fornecidos pelo Azure, consulte a secção seguinte para uma resolução de problemas.  

> [!Tip]
> Pode atualizar manualmente os registos DNS do seu ambiente de origem editando o ficheiro de anfitriões DNS no seu aparelho no local com o recurso de ligação privada FQDNs e os seus endereços IP privados associados. Esta opção é recomendada apenas para testes. <br/>  


### <a name="validate-the-private-dns-zone"></a>Validar a Zona Privada de DNS   
Se a resolução do DNS não estiver a funcionar como descrito na secção anterior, poderá haver um problema com a sua Zona Privada de DNS.  

#### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Confirme que existe o recurso privado de DNS Zone  
Por padrão, a Azure Migrate também cria uma zona privada de DNS correspondente ao subdomínio 'privatelink' para cada tipo de recurso. A zona privada de DNS será criada no mesmo grupo de recursos Azure que o grupo privado de recursos de ponto final. O grupo de recursos Azure deve conter recursos privados da zona de DNS com o seguinte formato:
- privatelink.vaultcore.azure.net para o cofre da chave 
- privatelink.blob.core.windows.net para a conta de armazenamento
- privatelink.siterecovery.windowsazure.com para o cofre dos serviços de recuperação (para replicações hyper-V e baseadas em agentes)
- privatelink.prod.migration.windowsazure.com - projeto migrar, projeto de avaliação e local de descoberta.   

A zona privada de DNS será criada automaticamente pela Azure Migrate (exceto para a conta de armazenamento cache/replicação selecionada pelo utilizador). Pode localizar a zona privada de DNS ligada navegando na página de ponto final privado e selecionando configurações de DNS. Você deve ver a zona privada de DNS sob a secção privada de integração de DNS. 

![Screenshot de configuração do DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration.png)  

Se a zona DNS não estiver presente (como mostrado abaixo), [crie um novo recurso Private DNS Zone.](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal)  

![Criar uma zona privada de DNS](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone.png) 

#### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Confirme que a Zona Privada de DNS está ligada à rede virtual  
A zona privada de DNS deve ser ligada à rede virtual que contém o ponto final privado para a consulta DNS para resolver o endereço IP privado do ponto final de recursos. Se a zona privada de DNS não estiver ligada à Rede Virtual correta, qualquer resolução DNS dessa rede virtual ignorará a zona privada de DNS.   

Navegue para o recurso de zona dNS privado no portal Azure e selecione as ligações de rede virtual a partir do menu esquerdo. Devia ver as redes virtuais ligadas.

![Ver links de rede virtuais](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links.png) 

Isto mostrará uma lista de links, cada um com o nome de uma rede virtual na sua subscrição. A rede virtual que contém o recurso Private Endpoint deve ser listada aqui. Caso contrário, [siga este artigo](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) para ligar a zona privada do DNS a uma rede virtual.    

Uma vez que a zona privada de DNS esteja ligada à rede virtual, os pedidos dns originários da rede virtual procurarão registos DNS na zona privada do DNS. Isto é necessário para uma resolução correta de endereço para a rede virtual onde o ponto final privado foi criado.   

#### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Confirme que a zona privada de DNS contém os registos A certos 

Vá para a zona privada do DNS que quer resolver problemas. A página 'Visão Geral' mostra todos os registos DNS dessa zona privada de DNS. Verifique se existe um registo DNS A para o recurso. O valor do registo A (o endereço IP) deve ser o endereço IP privado dos recursos. Se encontrar o registo A com o endereço IP errado, deve remover o endereço IP errado e adicionar um novo. Recomenda-se que retire todo o disco A e adicione um novo, e faça uma descarga de DNS no aparelho de origem no local.   

Um exemplo ilustrativo para a conta de armazenamento DNS Um registo na zona privada de DNS:

![Registos dns](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Um exemplo ilustrativo para os microserviços de cofre dos Serviços de Recuperação DNS A registos na zona privada de DNS: 

![Registos de DNS para cofre dos Serviços de Recuperação](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records.png)   

>[!Note]
> Quando remove ou modifica um registo A, a máquina pode ainda resolver-se para o antigo endereço IP, uma vez que o valor TTL (Tempo para Viver) pode ainda não ter expirado.  

#### <a name="other-things-that-may-affect-private-link-connectivity"></a>Outras coisas que podem afetar a conectividade de ligação privada  

Esta é uma lista não exaustiva de itens que podem ser encontrados em cenários avançados ou complexos: 

- Definições de firewall, quer a Firewall Azure ligada à rede Virtual, quer uma solução de firewall personalizada implantada na máquina do aparelho.  
- O espreitamento da rede, que pode ter impacto nos servidores DNS utilizados e na forma como o tráfego é encaminhado.  
- As soluções de gateway personalizado (NAT) podem ter impacto na forma como o tráfego é encaminhado, incluindo o tráfego de consultas DNS.

Para obter mais informações, reveja o [guia de resolução de problemas para problemas de conectividade Private Endpoint.](https://docs.microsoft.com/azure/private-link/troubleshoot-private-endpoint-connectivity)  

## <a name="next-steps"></a>Passos seguintes 
- [Complete o processo de migração](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#complete-the-migration) e reveja as [melhores práticas pós-migração.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#post-migration-best-practices)