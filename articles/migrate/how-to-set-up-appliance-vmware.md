---
title: Crie um aparelho Azure Migrate para VMware
description: Saiba como configurar um aparelho Azure Migrate para avaliar e migrar servidores em ambiente VMware.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: c2ffa85ed6cb007dd766d4517a86783d21d4913e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110513"
---
# <a name="set-up-an-appliance-for-servers-in-vmware-environment"></a>Configurar um aparelho para servidores em ambiente VMware

Siga este artigo para configurar o aparelho Azure Migrate para avaliação com a ferramenta [Azure Migrate:Discovery e assessment,](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) e para migração sem agente utilizando a ferramenta [migração Azure Migrate:Server.](migrate-services-overview.md#azure-migrate-server-migration-tool)

O [aparelho Azure Migrate](migrate-appliance.md) é um aparelho leve utilizado pelo Azure Migrate:Discovery and assessment and Server Migration para descobrir servidores em execução no vCenter Server, enviar a configuração do servidor e metadados de desempenho para o Azure, e para a replicação de servidores usando migração sem agente.

Pode utilizar o aparelho utilizando um par de métodos:

- Crie um servidor no vCenter Server utilizando um modelo OVA descarregado. Este é o método descrito neste artigo.
- Instale o aparelho num servidor existente utilizando um script instalador PowerShell. [Este método](deploy-appliance-script.md) deve ser utilizado se não puder utilizar o modelo OVA, ou se estiver no Governo de Azure.

Depois de criar o aparelho, verifique se pode ligar-se ao Azure Migrate: Descoberta e avaliação, registá-lo com o projeto e configurar o aparelho para iniciar a descoberta.

## <a name="deploy-with-ova"></a>Implementar com OVA

Para configurar o aparelho utilizando um modelo OVA:

1. Forneça um nome de aparelho e gere uma chave de projeto no portal.
1. Descarregue um ficheiro de modelo OVA e importe-o para o vCenter Server. Verifique se o OVA está seguro.
1. Crie o VM do aparelho a partir do ficheiro OVA , e verifique se pode ligar-se ao Azure Migrate.
1. Configure o aparelho pela primeira vez e registe-o com o projeto utilizando a chave do projeto.

### <a name="1-generate-the-project-key"></a>1. Gerar a chave do projeto

1. Em **Objetivos de Migração**  >  **Servidores**  >  **Azure Migrate: Descoberta e avaliação**, selecione **Discover**.
2. In **Discover servers**  >  **Are your servers virtualized?** 
3. Em **1:Gere a tecla do projeto,** forneça um nome para o aparelho Azure Migrate que irá configurar para a descoberta de servidores no seu ambiente VMware. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique na **chave Gerar** para iniciar a criação dos recursos Azure necessários. Não feche a página Discover durante a criação de recursos.
1. Após a criação bem sucedida dos recursos Azure, é gerada uma chave de projeto**
1. Copie a chave pois necessitará para completar o registo do aparelho durante a sua configuração.

### <a name="2-download-the-ova-template"></a>2. Descarregue o modelo OVA

Em **2: Descarregue o aparelho Azure Migrate,** selecione o . Ficheiro OVA e clique no **Download**.

### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro OVA está seguro, antes de o implementar:

1. No servidor para o qual descarregou o ficheiro, abra uma janela de comando do administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Utilização de exemplo: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Verifique as versões mais recentes do aparelho e os valores do haxixe:

    - Para a nuvem pública de Azure:
    
        **Algoritmo** | **Transferência** | **SHA256**
        --- | --- | ---
        VMware (11.9 GB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae8108328e8f3a7933d7835ecd871d1b17f367621ce3c74


### <a name="3-create-the-appliance-server"></a>3. Criar o servidor do aparelho

Importe o ficheiro descarregado e crie um servidor em ambiente VMware

1. Na consola vSphere Client, clique em   >  **Ficheiros Implementar OVF Modelo**.
2. No Assistente de Modelo de OVF de implementação > **Fonte**, especifique a localização do ficheiro OVA.
3. No **Nome** e **Localização**, especifique um nome amigável para o servidor. Selecione o objeto de inventário no qual o servidor será hospedado.
5. No **Host/Cluster**, especifique o anfitrião ou o cluster no qual o servidor irá funcionar.
6. No **Armazenamento**, especifique o destino de armazenamento para o servidor.
7. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
8. No **Mapeamento de Rede,** especifique a rede à qual o servidor se ligará. A rede precisa de conectividade na Internet, para enviar metadados para a Azure Migrate.
9. Reveja e confirme as definições e, em seguida, clique em **Concluir**.


### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o servidor do aparelho pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)


### <a name="4-configure-the-appliance"></a>4. Configurar o aparelho

Coloque o aparelho pela primeira vez.

> [!NOTE]
> Se configurar o aparelho utilizando um [**script PowerShell**](deploy-appliance-script.md) em vez do OVA descarregado, os dois primeiros passos deste procedimento não são relevantes.

1. Na consola vSphere Client, clique com o botão direito no servidor e, em seguida, selecione **Open Console**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um browser em qualquer servidor que possa ligar ao servidor do aparelho e abra o URL do gestor de configuração do aparelho: `https://appliance name or IP address: 44368` .

   Em alternativa, pode abrir o gestor de configuração a partir do ambiente de trabalho do servidor do aparelho selecionando o atalho para o gestor de configuração.
1. Aceite os termos da **licença** e leia as informações de terceiros.
1. No gestor de configuração > **Configurar pré-requisitos,** faça o seguinte:
   - **Conectividade**: O aparelho verifica se o servidor tem acesso à Internet. Se o servidor utilizar um representante:
     - Clique no **representante de configuração** para especificar o endereço de procuração no formulário `http://ProxyIPAddress` ou na porta de `http://ProxyFQDN` escuta.
     - Especifique as credenciais se o proxy precisar de autenticação.
     - Apenas é suportado o proxy HTTP.
     - Se tiver adicionado detalhes de procuração ou desativado o proxy e/ou autenticação, clique em **Guardar** para ativar novamente a verificação de conectividade.
   - **Sincronização de tempo**: O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta funcione corretamente.
   - **Instalação de atualizações**: O aparelho assegura a instalação das atualizações mais recentes. Depois de concluída a verificação, pode clicar nos **serviços do aparelho ver** o estado e as versões dos serviços em execução no servidor do aparelho.
   - **Instalação VDDK**: O aparelho verifica se o Kit de Desenvolvimento de Discos Virtuais VMware vSphere (VDDK) está instalado. Se não estiver instalado, faça o download do VDDK 6.7 da VMware e extraia o conteúdo zip descarregado para o local especificado no aparelho, conforme indicado nas **instruções de instalação**.

     A migração do servidor Azure Migrate usa o VDDK para replicar servidores durante a migração para Azure. 
1. Se desejar, pode **repetir os pré-requisitos** a qualquer momento durante a configuração do aparelho para verificar se o aparelho cumpre todos os requisitos.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Painel 1 no gestor de configuração do aparelho":::


## <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Cole a chave do **projeto** copiada do portal. Se não tiver a chave, vá ao **Discovery e a avaliar> Descubra> Gerir os aparelhos existentes**, selecione o nome do aparelho que forneceu no momento da geração de chaves e copie a chave correspondente.
1. Necessitará de um código de dispositivo para autenticar com o Azure. Clicar no **Login** abrirá um código modal com o código do dispositivo, como mostrado abaixo.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modal mostrando o código do dispositivo":::

1. Clique no **código copy & Iniciar sessão** para copiar o código do dispositivo e abrir um pedido de Login Azure num novo separador de navegador. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
1. No novo separador, cole o código do dispositivo e inscreva-se utilizando o seu nome de utilizador Estaure e a palavra-passe.
   
   O s-in com um PIN não é suportado.
3. Caso feche o separador de login acidentalmente sem iniciar sessão, é necessário atualizar o separador de navegador do gestor de configuração do aparelho para ativar novamente o botão Iniciar sessão.
1. Depois de iniciar sessão com sucesso, volte ao separador anterior com o gestor de configuração do aparelho.
1. Se a conta de utilizador Azure utilizada para a exploração madeireira tiver as permissões certas sobre os recursos Azure criados durante a geração chave, o registo do aparelho será iniciado.
1. Depois de o aparelho estar registado com sucesso, pode ver os dados do registo clicando nos **detalhes do Ver.**

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Painel 2 no gestor de configuração do aparelho":::

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

### <a name="provide-vcenter-server-details"></a>Fornecer detalhes do servidor vCenter

O aparelho precisa de se ligar ao servidor vCenter para descobrir a configuração e os dados de desempenho dos servidores.

1. No **passo 1: Forneça credenciais do servidor vCenter**, clique em **Adicionar credenciais** para especificar um nome amigável para credenciais, adicionar **nome de utilizador** e **palavra-passe** para a conta vCenter Server que o aparelho utilizará para descobrir servidores em execução no servidor vCenter.
    - Deveria ter criado uma conta com as permissões necessárias, conforme coberto neste artigo acima.
    - Se pretender estender a descoberta a objetos VMware específicos (centros de dados vCenter Server, clusters, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou servidores individuais.), reveja as instruções [deste artigo](set-discovery-scope.md) para restringir a conta utilizada pela Azure Migrate.
1. No **passo 2: Forneça detalhes do servidor vCenter**, clique na **fonte de descoberta Adicionar** para selecionar o nome amigável para credenciais a partir do drop-down, especificar o endereço **IP/FQDN** do servidor vCenter. Pode deixar a **Porta** para predefinição (443) ou especificar uma porta personalizada na qual o vCenter Server ouve e clica em **Guardar**.
1. Ao clicar em **Guardar**, o aparelho tentará validar a ligação ao servidor vCenter com as credenciais fornecidas e mostrar o **estado de Validação** na tabela contra o endereço IP/FQDN do servidor vCenter.
1. Pode **revalidar** a conectividade ao vCenter Server a qualquer momento antes de iniciar a descoberta.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Painel no gestor de configuração do aparelho para detalhes do servidor vCenter":::

### <a name="provide-server-credentials"></a>Fornecer credenciais de servidor

No **Passo 3: Fornecer credenciais de servidor para realizar inventário de software, análise de dependência de agente e descoberta de instâncias e bases de dados do SQL Server,** pode optar por fornecer múltiplas credenciais de servidor ou se não quiser aproveitar estas funcionalidades, pode optar por saltar o passo e proceder à descoberta do vCenter Server. Pode mudar a sua intenção a qualquer momento.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Painel 3 no gestor de configuração do aparelho para detalhes do servidor":::


Se quiser aproveitar estas funcionalidades, pode fornecer credenciais de servidor seguindo os passos abaixo. O aparelho tentará mapear automaticamente as credenciais para os servidores para executar as funcionalidades de descoberta.

- Pode adicionar credenciais de servidor clicando no botão **Adicionar Credenciais.** Isto abrirá um modelol onde pode escolher o **tipo de Credenciais** a partir do drop-down.
- Pode fornecer credenciais de autenticação de domínio/ Windows (não domínio)/ Linux (não domínio)/ Credenciais de autenticação do SQL Server. [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como as lidamos.
- Para cada tipo de credenciais, é necessário especificar um nome amigável para credenciais, adicionar **username** e **Password** e clicar em **Guardar**.
- Se escolher credenciais de domínio, também terá de especificar o FQDN para o domínio. O FQDN é necessário para validar a autenticidade das credenciais com o Diretório Ativo desse domínio.
- Reveja as [permissões necessárias](add-server-credentials.md#required-permissions) na conta para a descoberta de aplicações instaladas, análise de dependência de agentes ou para descoberta de instâncias e bases de dados do SQL Server.
- Se quiser adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para guardar e adicionar mais credenciais.
- Ao clicar em **Guardar** ou **Adicionar mais,** o aparelho valida as credenciais de domínio com o Diretório Ativo do domínio para a sua autenticidade. Isto é feito para evitar qualquer bloqueio de conta quando o aparelho faz várias iterações para mapear credenciais para os respetivos servidores.
- Pode ver o **estado de validação** de todas as credenciais de domínio na tabela de credenciais. Apenas as credenciais de domínio serão validadas.
- Se a validação falhar, pode clicar no estado **falhado** para ver o erro encontrado e clicar em **credenciais de Revalidato** depois de corrigir o problema para validar novamente as credenciais de domínio falhadas.
    :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Painel 3 no gestor de configuração do aparelho para fornecer múltiplas credenciais":::

### <a name="start-discovery"></a>Iniciar a deteção

1. Clique em **Iniciar a descoberta**, para iniciar a descoberta do servidor vCenter. Após a descoberta ter sido iniciada com sucesso, pode verificar o estado de descoberta com o endereço IP/FQDN do vCenter Server na tabela de fontes.
1. Se tiver fornecido credenciais de servidor, o inventário de software (descoberta de aplicações instaladas) será iniciado automaticamente após a descoberta do vCenter Server ter concluído. O inventário de software é realizado uma vez a cada 12 horas.
1. [O inventário](how-to-discover-applications.md) de software identifica as instâncias do SQL Server em execução nos servidores e utilizando as informações, o aparelho tenta ligar-se às instâncias através da autenticação do Windows ou das credenciais de autenticação do SQL Server fornecidas no aparelho e recolher dados nas bases de dados do SQL Server e suas propriedades. A descoberta SQL é realizada uma vez a cada 24 horas.
1. Durante o inventário do software, as credenciais de servidores adicionados serão iteradas contra servidores e validadas para análise de dependência de agente. Pode ativar a análise de dependência de agentes para servidores a partir do portal. Apenas os servidores onde a validação é bem sucedida podem ser selecionados para permitir a análise da dependência de agentes.

A descoberta funciona da seguinte forma:

- Leva cerca de 15 minutos para o inventário de servidores descobertos aparecer no portal.
- A descoberta de aplicações instaladas pode demorar algum tempo. A duração depende do número de servidores descobertos. Para 500 servidores, leva aproximadamente uma hora para que o inventário descoberto apareça no portal Azure Migrate.
- Após a descoberta dos servidores estar concluída, pode ativar a análise de dependência sem agente nos servidores a partir do portal.
- Os dados do SQL Server e das bases de dados começarão a aparecer no portal dentro de 24 horas a partir do início da descoberta.

## <a name="next-steps"></a>Passos seguintes

Reveja os tutoriais para [avaliação de VMware](./tutorial-assess-vmware-azure-vm.md) e [migração sem agentes.](tutorial-migrate-vmware.md)
