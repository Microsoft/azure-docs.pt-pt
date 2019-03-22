---
title: Sobre a aplicação Recoletora no Azure Migrate | Documentos da Microsoft
description: Fornece informações sobre a aplicação Recoletora no Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 78ef88d0710e8a82d30fa26b60073aa75e363c15
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315305"
---
# <a name="about-the-collector-appliance"></a>Sobre a aplicação Recoletora

 Este artigo fornece informações sobre o Recoletor do Azure Migrate.

Recoletor do Azure Migrate é uma aplicação simples que pode ser utilizado para detetar um ambiente de vCenter no local para fins de avaliação com o [do Azure Migrate](migrate-overview.md) serviço específico, antes da migração para o Azure.  

## <a name="discovery-method"></a>Método de deteção

Anteriormente, eram duas opções para a aplicação recoletora, a deteção de uso individual e a deteção contínua. O modelo de deteção de uso individual foi agora preterido como ele contava com as definições de estatísticas do vCenter Server para a recolha de dados de desempenho (definições de estatísticas necessária para ser definida como nível 3) e também coletados contadores médios (em vez de pico) que resultou em dimensionamento insuficientemente. O modelo de deteção contínua garante a recolha de dados detalhados e resulta em dimensionamento preciso devido a recolha de contadores de pico de. Segue-se como ele funciona:

A aplicação recoletora fica continuamente conectada ao projeto do Azure Migrate e continuamente recolhe dados de desempenho de VMs.

- O recoletor perfis continuamente o ambiente no local para recolher dados de utilização em tempo real a cada 20 segundos.
- A aplicação agrega os exemplos de 20 segundos e cria um único ponto de dados a cada 15 minutos.
- Para criar os dados ponto a aplicação da seleciona o valor de pico de exemplos de 20 segundos e envia-os para o Azure.
- Este modelo não depende das definições de estatísticas do vCenter Server para recolher dados de desempenho.
- Pode parar contínua de criação de perfis em qualquer altura do Recoletor.

**Avaliações rápidas:** Com a aplicação de deteção contínua, uma vez que a deteção esteja concluída (demora duas horas, consoante o número de VMs), pode criar avaliações de imediato. Uma vez que a recolha de dados de desempenho é iniciado quando pode iniciar a deteção, se estiver à procura de avaliações rápidas, selecione o critério de tamanho na avaliação como *como no local*. Para obter avaliações baseado no desempenho, é recomendado aguardar pelo menos um dia após iniciar deteção para obter recomendações de tamanho fiável.

A aplicação só recolhe dados de desempenho continuamente, ele não detecta qualquer alteração de configuração no ambiente no local (ou seja, a adição de VM, eliminação, a adição de disco etc.). Se houver uma alteração de configuração no ambiente no local, pode fazer o seguinte para refletir as alterações no portal:

- Adição de itens (VMs, discos, núcleos, etc.): Para refletir estas alterações no portal do Azure, pode parar a deteção a partir da aplicação e, em seguida, inicie-o novamente. Isto irá garantir que as alterações são atualizadas no projeto do Azure Migrate.

- Eliminação das VMs: A forma como a aplicação foi concebida, a eliminação de VMs não será refletida, mesmo se parar e iniciar a deteção. Isto acontece porque os dados das deteções subsequentes são anexados às deteções mais antigas e não são substituídos. Neste caso, pode simplesmente ignorar a VM no portal, ao removê-la do seu grupo e recalcular a avaliação.

> [!NOTE]
> A aplicação de deteção de uso individual foi agora preterida como esse método baseou-se no vCenter definições de estatísticas do servidor para a disponibilidade de ponto de dados de desempenho e coletados contadores de desempenho médio que resultou em insuficientemente dimensionamento de VMs para a migração para o Azure.

## <a name="deploying-the-collector"></a>Implementar o Recoletor

Implementar a aplicação Recoletora através de um modelo OVF:

- Transfira o modelo OVF a partir de um projeto do Azure Migrate no portal do Azure. Importar o ficheiro transferido para o vCenter Server, para configurar a aplicação Recoletora VM.
- Do OVF, VMware define uma VM com 8 núcleos, 16 GB de RAM e um disco de 80 GB. O sistema operacional é Windows Server 2016 (64 bits).
- Ao executar o Recoletor, execute um número de verificações de pré-requisitos para se certificar de que o coletor pode ligar ao Azure Migrate.

- [Saiba mais](tutorial-assessment-vmware.md#create-the-collector-vm) sobre como criar o Recoletor.


## <a name="collector-prerequisites"></a>Pré-requisitos do recoletor

O Recoletor tem de passar algumas verificações de pré-requisitos para garantir que pode estabelecer ligação ao serviço Azure Migrate através da internet e detetados de carregamento de dados.

- **Certifique-se na cloud do Azure**: O Recoletor precisa saber a cloud do Azure para o qual pretende migrar.
    - Se estiver a planear migrar para a cloud do Azure Government, selecione Azure Government.
    - Selecione Global do Azure se estiver a planear migrar para a cloud do Azure comercial.
    - Com base na cloud especificada aqui, a aplicação irá enviar metadados detetados para os respetivos pontos finais.
- **Verifique a ligação de internet**: O coletor pode ligar à internet diretamente ou através de um proxy.
    - A verificação de pré-requisitos verifica a conetividade à [URLs obrigatórios e opcionais](#urls-for-connectivity).
    - Se tiver uma ligação direta à internet, nenhuma ação específica é necessária, que não seja de certificar-se de que o coletor de contactar os URLs necessários.
    - Se estiver a ligar através de um proxy, tenha em atenção os requisitos abaixo.
- **Certifique-se a sincronização de hora**: O Recoletor devem sincronizados com o servidor de horas da internet para garantir que os pedidos para o serviço são autenticados.
    - A portal.azure.com url deve ser acessível a partir do Recoletor para que o tempo pode ser validado.
    - Se a máquina não está sincronizada, terá de alterar a hora de relógio na VM do Recoletor de acordo com a hora atual. Para fazer isso abra uma linha de administrador na VM, execute **w32tm /tz** para verificar o fuso horário. Execute **w32tm /resync** para sincronizar a hora.
- **Verificar a execução do serviço de recoletor**:  O serviço do Recoletor do Azure Migrate deve ser executado na VM do Recoletor.
    - Este serviço é iniciado automaticamente quando a máquina é inicializada.
    - Se o serviço não está em execução, inicie-o do painel de controle.
    - O serviço do Recoletor liga-se ao vCenter Server, recolhe os dados de metadados e o desempenho da VM e envia-os para o serviço Azure Migrate.
- **Verificar o VMware PowerCLI 6.5 instalado**: O módulo do PowerShell do VMware PowerCLI 6.5 tem de ser instalado na VM do Recoletor, para que ele possa comunicar com o vCenter Server.
    - Se o coletor pode aceder aos URLs necessários para instalar o módulo, é instalar automaticamente durante a implementação do Recoletor.
    - Se o Recoletor não é possível instalar o módulo durante a implementação, deve instalá-lo manualmente.
- **Verifique a ligação ao vCenter Server**: O Recoletor tem de ser capaz de servidor vCenter e consulta por VMs, metadados e contadores de desempenho. [Verificar pré-requisitos](#connect-to-vcenter-server) para ligar.


### <a name="connect-to-the-internet-via-a-proxy"></a>Ligar à internet através de um proxy

- Se o servidor proxy requer autenticação, pode especificar o nome de utilizador e palavra-passe quando configurou o Recoletor.
- Endereço de IP/FQDN do servidor Proxy deve especificado como *http:\//IPaddress* ou *http:\//FQDN*.
- Apenas é suportado o proxy HTTP. Servidores proxy baseado em HTTPS não são suportados pelo Recoletor.
- Se o servidor proxy é um proxy de interceção, tem de importar o certificado de proxy para a VM do Recoletor.
  1. Na VM do recoletor, aceda a **Menu Iniciar** > **gerir certificados de computador**.
  2. Na ferramenta de certificados, sob **certificados - Computador Local**, localize **fabricantes fidedignos** > **certificados**.

      ![Ferramenta de certificados](./media/concepts-intercepting-proxy/certificates-tool.png)

  3. Copie o certificado de proxy para a VM do recoletor. Poderá ter de obtê-lo a partir do seu administrador de rede.
  4. Clique duas vezes para abrir o certificado e clique em **instalar certificado**.
  5. No Assistente para importar certificados > localização da Store, escolha **computador Local**.

     ![Localização do arquivo de certificados](./media/concepts-intercepting-proxy/certificate-store-location.png)

  6. Selecione **colocar todos os certificados no seguinte arquivo** > **procurar** > **fabricantes fidedignos**. Clique em **concluir** para importar o certificado.

     ![Arquivo de certificados](./media/concepts-intercepting-proxy/certificate-store.png)

  7. Verifique que o certificado é importado conforme esperado e verifique que a verificação de pré-requisitos a conectividade internet funcionar conforme esperado.


### <a name="urls-for-connectivity"></a>URLs para conectividade

A verificação de conectividade é validada ao ligar a uma lista de URLs.

**URL** | **Detalhes**  | **Verificação de pré-requisitos**
--- | --- | ---
*.portal.azure.com | Aplicável para o Azure Global. Verificações de conectividade com o serviço do Azure e a sincronização de hora. | Acesso a URL é necessário.<br/><br/> A verificação de pré-requisitos falha se nenhuma conectividade.
*.portal.azure.us | Aplicável apenas para o Azure Government. Verificações de conectividade com o serviço do Azure e a sincronização de hora. | Acesso a URL é necessário.<br/><br/> A verificação de pré-requisitos falha se nenhuma conectividade.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Utilizado para transferir o módulo do PowerShell vCenter PowerCLI. | É necessário o acesso aos URLs.<br/><br/> Verificação de pré-requisitos não falhará.<br/><br/> Instalação do módulo automática na VM do Recoletor irá falhar. Terá de instalar o módulo manualmente numa máquina que tenha conectividade à internet e, em seguida, copie os módulos para a aplicação. [Saiba mais indo para o passo n. º 4 neste guia de resolução de problemas](https://docs.microsoft.com/azure/migrate/troubleshooting-general#error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception).


### <a name="install-vmware-powercli-module-manually"></a>Instalar manualmente o módulo de VMware PowerCLI

1. Instala o módulo usando [essas etapas](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). Estes passos descrevem a instalação tanto online quanto offline.
2. Se a VM do Recoletor está offline e instalar o módulo num computador diferente com acesso à internet, terá de copiar os ficheiros de VMware.* desse computador para a VM do Recoletor.
3. Após a instalação, pode reiniciar as verificações de pré-requisitos para confirmar se o PowerCLI está instalado.

### <a name="connect-to-vcenter-server"></a>Ligar ao vCenter Server

O Recoletor liga-se ao vCenter Server e consultas de metadados da VM e contadores de desempenho. Eis o que precisa para a ligação.

- Apenas vCenter Server 5.5, 6.0, 6.5 e 6.7 são suportadas as versões.
- Precisa de uma conta só de leitura com as permissões resumidas abaixo para a deteção. Pode ser acessados apenas acessíveis com a conta de datacenters para a deteção.
- Por predefinição, vai ligar ao vCenter Server com um endereço IP ou FQDN. Se o vCenter Server escuta numa porta diferente, se conectar a ela utilizando a forma *IPAddress:Port_Number* ou *FQDN:Port_Number*.
- O Recoletor deve ter uma rede linha Visual para o servidor vCenter.

#### <a name="account-permissions"></a>Permissões de conta

**Conta** | **Permissões**
--- | ---
Pelo menos uma conta de utilizador só de leitura | Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura   


## <a name="collector-communications"></a>Comunicações de recoletor

O recoletor comunica conforme resumido no diagrama e a tabela seguinte.

![Diagrama de comunicação do recoletor](./media/tutorial-assessment-vmware/portdiagram.PNG)


**Recoletor comunica com** | **Porta** | **Detalhes**
--- | --- | ---
Serviço do Azure Migrate | TCP 443 | Recoletor se comunica com o serviço Azure Migrate através de SSL 443.
vCenter Server | TCP 443 | O Recoletor tem de ser capaz de comunicar com o vCenter Server.<br/><br/> Por padrão, ele se conecta ao vCenter em 443.<br/><br/> Se o vCenter Server escuta numa porta diferente, essa porta deve estar disponível como porta de saída no Recoletor.
RDP | TCP 3389 |

## <a name="collected-metadata"></a>Metadados recolhidos

A aplicação recoletora Deteta os seguintes metadados de configuração para cada VM. Os dados de configuração para as VMs estão disponíveis uma hora depois de iniciar a deteção.

- Nome de exibição VM (no vCenter Server)
- Caminho de inventário da VM (o anfitrião/pasta no vCenter Server)
- Endereço IP
- Endereço MAC
- Sistema operativo
- Número de núcleos, discos, NICs
- Tamanho da memória, tamanhos de disco
- Contadores de desempenho da VM, disco e rede.

### <a name="performance-counters"></a>Contadores de desempenho

 A aplicação recoletora recolhe os seguintes contadores de desempenho para cada VM do anfitrião ESXi num intervalo de 20 segundos. Esses contadores são contadores do vCenter e embora a terminologia diga média, os exemplos de 20 segundos são contadores em tempo real. Os dados de desempenho para as VMs começaram a se tornar disponível no portal de duas horas depois de ter iniciada a deteção. Recomenda-se vivamente que aguarde, pelo menos, um dia antes de criar com base em desempenho avaliações para obter recomendações de tamanho adequado precisas. Se estiver à procura de instantâneos, pode criar avaliações com critério de dimensionamento como *como no local* qual não irá considerar os dados de desempenho para o dimensionamento certo.

**Contador** |  **Impacto na avaliação**
--- | ---
cpu.usage.average | Tamanho VM recomendados e os custos  
mem.usage.average | Tamanho VM recomendados e os custos  
virtualDisk.read.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.write.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.numberReadAveraged.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.numberWriteAveraged.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
net.received.average | Calcula o tamanho da VM                          
net.transmitted.average | Calcula o tamanho da VM     

A lista completa dos contadores de VMware recolhidos pelo Azure Migrate está disponível abaixo:

**Categoria** |  **metadados** | **ponto de dados do vCenter**
--- | --- | ---
Detalhes da máquina | ID da VM | vm.Config.InstanceUuid
Detalhes da máquina | o nome da VM | vm.Config.Name
Detalhes da máquina | ID do servidor do vCenter | VMwareClient.InstanceUuid
Detalhes da máquina |  Descrição da VM |  vm.Summary.Config.Annotation
Detalhes da máquina | Nome do produto de licença | vm.Client.ServiceContent.About.LicenseProductName
Detalhes da máquina | Tipo de sistema operativo | vm.Summary.Config.GuestFullName
Detalhes da máquina | Versão do sistema operativo | vm.Summary.Config.GuestFullName
Detalhes da máquina | Tipo de arranque | vm.Config.Firmware
Detalhes da máquina | Número de núcleos | vm.Config.Hardware.NumCPU
Detalhes da máquina | Megabytes de memória | vm.Config.Hardware.MemoryMB
Detalhes da máquina | Número de discos | VM. Config.Hardware.Device.ToList(). FindAll(x => x is VirtualDisk).count
Detalhes da máquina | Lista de tamanho de disco | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk)
Detalhes da máquina | Lista de adaptadores de rede | VM. Config.Hardware.Device.ToList(). FindAll (x = > x é VirtualEthernetCard)
Detalhes da máquina | Utilização da CPU | cpu.usage.average
Detalhes da máquina | Utilização da memória | mem.usage.average
Detalhes do disco (por disco) | Valor de chave de disco | disco. Chave
Detalhes do disco (por disco) | Número de unidade de disco | disk.UnitNumber
Detalhes do disco (por disco) | Valor de chave do controlador de disco | disk.ControllerKey.Value
Detalhes do disco (por disco) | Gigabytes aprovisionados | virtualDisk.DeviceInfo.Summary
Detalhes do disco (por disco) | Nome do disco | Este valor é gerado usando o disco. UnitNumber, o disco. Chave e o disco. ControllerKey.Value
Detalhes do disco (por disco) | Número de operações de leitura por segundo | virtualDisk.numberReadAveraged.average
Detalhes do disco (por disco) | Número de operações de escrita por segundo | virtualDisk.numberWriteAveraged.average
Detalhes do disco (por disco) | Megabytes por segundo de débito de leitura | virtualDisk.read.average
Detalhes do disco (por disco) | Megabytes por segundo de débito de escrita | virtualDisk.write.average
Detalhes do adaptador de rede (por NIC) | Nome do adaptador de rede | nic.Key
Detalhes do adaptador de rede (por NIC) | Endereço MAC | ((VirtualEthernetCard)nic).MacAddress
Detalhes do adaptador de rede (por NIC) | Endereços IPv4 | vm.Guest.Net
Detalhes do adaptador de rede (por NIC) | Endereços IPv6 | vm.Guest.Net
Detalhes do adaptador de rede (por NIC) | Megabytes por segundo de débito de leitura | net.received.average
Detalhes do adaptador de rede (por NIC) | Megabytes por segundo de débito de escrita | net.transmitted.average
Detalhes do caminho de inventário | Name | container.GetType().Name
Detalhes do caminho de inventário | Tipo de objeto subordinado | container.ChildType
Detalhes do caminho de inventário | Detalhes de referência | container.MoRef
Detalhes do caminho de inventário | Caminho de inventário completo | contentor. Dê um nome com o caminho completo
Detalhes do caminho de inventário | Detalhes de principal | Container.Parent
Detalhes do caminho de inventário | Detalhes da pasta para cada VM | ((Folder)container).ChildEntity.Type
Detalhes do caminho de inventário | Detalhes do Centro de dados para cada pasta de VM | ((Datacenter)container).VmFolder
Detalhes do caminho de inventário | Detalhes do Centro de dados para cada pasta de anfitrião | ((Datacenter)container).HostFolder
Detalhes do caminho de inventário | Detalhes do cluster para cada anfitrião | ((ClusterComputeResource)container).Host)
Detalhes do caminho de inventário | Detalhes do anfitrião para cada VM | ((HostSystem)container).Vm


## <a name="securing-the-collector-appliance"></a>Proteger a aplicação Recoletora

Recomendamos os seguintes passos para proteger a aplicação Recoletora:

- Não partilhe ou perdas de palavras-passe de administrador com partes não autorizadas.
- Encerre a aplicação quando não está em utilização.
- Coloca a aplicação numa rede segura.
- Após a migração estiver concluída, elimine a instância da aplicação.
- Além disso, após a migração, também elimine os ficheiros de cópia de segurança de discos (VMDKs), como os discos podem ter as credenciais do vCenter em cache nos mesmos.

## <a name="os-license-in-the-collector-vm"></a>Licença de SO na VM do recoletor

O recoletor vem com uma licença de avaliação do Windows Server 2012 R2 que é válida por 180 dias. Se o período de avaliação está prestes a expirar para a sua VM do recoletor, recomenda-se para transferir uma nova OVA e criar um novo dispositivo.

## <a name="updating-the-os-of-the-collector-vm"></a>A atualizar o SO da VM do Recoletor

Apesar da aplicação recoletora ter uma licença de avaliação de 180 dias, precisa atualizar continuamente o sistema operacional na aplicação para evitar desligar automaticamente para baixo da aplicação.

- Se o Recoletor não é atualizado nos últimos 60 dias, este começa a encerrar a máquina automaticamente.
- Se estiver a executar uma deteção, a máquina não ser desativada, mesmo se passaram 60 dias. A máquina será desativada depois de concluída a deteção.
- Se já usou o Recoletor durante mais de 60 dias, recomendamos que mantenha a máquina de atualização em todo o tempo pela atualização do Windows em execução.

## <a name="upgrading-the-collector-appliance-version"></a>Atualizar a versão da aplicação de Recoletor

Pode atualizar o Recoletor para a versão mais recente sem baixar o OVA novamente.

1. Transferir o [mais recente listado o pacote de atualização](concepts-collector-upgrade.md)
2. Para garantir que a correção transferida é segura, abra a janela de comando de administrador e execute o seguinte comando para gerar o hash para o ficheiro ZIP. O hash gerado deve corresponder com o hash mencionado em relação a versão específica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (example usage C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Copie o ficheiro zip para a máquina de virtual do recoletor de Azure Migrate (aplicação recoletora).
4. Com o botão direito no ficheiro zip e selecione extrair tudo.
5. Com o botão direito no Setup.ps1 e selecionar executar com o PowerShell e siga as instruções no ecrã para instalar a atualização.

## <a name="discovery-process"></a>Processo de deteção

Depois de configurar a aplicação, pode executar a deteção. Eis como funciona:

- Executar uma deteção por âmbito. Todas as VMs no caminho de inventário do vCenter especificado serão detetadas.
    - Definir um âmbito de cada vez.
    - O âmbito pode incluir 1500 VMs ou menos.
    - O âmbito pode ser um centro de dados, uma pasta ou um anfitrião ESXi.
- Depois de ligar ao vCenter Server, se conectar ao especificar um projeto de migração para a coleção.
- Deteção das VMs e seus dados de desempenho e de metadados são enviados para o Azure. Estas ações fazem parte de uma tarefa de coleção.
    - A aplicação Recoletora é atribuída um ID específico do Recoletor que é persistente para um determinado computador entre deteções.
    - Uma tarefa em execução é fornecida um ID de sessão específicos. O ID é alterado para cada tarefa de coleção e pode ser utilizado para resolução de problemas.

## <a name="next-steps"></a>Passos Seguintes

[Configurar uma avaliação para VMs de VMware no local](tutorial-assessment-vmware.md)
