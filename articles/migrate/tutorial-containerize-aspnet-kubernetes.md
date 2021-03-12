---
title: Contentorize & migram ASP.NET aplicações para Azure Kubernetes
description: Tutorial:Containerize & migrar ASP.NET aplicações para o Serviço Azure Kubernetes.
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: 6be6db2048ac5e671d8ab988ac2e15c08e900193
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103233672"
---
# <a name="containerize-aspnet-applications-and-migrate-to-azure-kubernetes-service"></a>Contentorizar aplicações de ASP.NET e migrar para o Serviço Azure Kubernetes

Neste artigo, você vai aprender a contentorizar aplicações ASP.NET e emigrá-las para [o Serviço Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/) usando a ferramenta Azure Migrate: App Containerization. O processo de contentorização não requer acesso à sua base de código e proporciona uma forma fácil de contentorizar as aplicações existentes. A ferramenta funciona utilizando o estado de funcionamento das aplicações num servidor para determinar os componentes da aplicação e ajuda-o a embalá-los numa imagem de recipiente. A aplicação contentorizada pode então ser implantada no Serviço Azure Kubernetes (AKS).

A ferramenta Azure Migrate: App Containerization suporta atualmente -

- A contentorização de ASP.NET aplicações e a sua implantação em contentores windows em AKS.
- Contentorizando as Aplicações Web java em Apache Tomcat (nos servidores Linux) e implantando-as em recipientes Linux em AKS. [Saiba mais](./tutorial-containerize-java-kubernetes.md)


A ferramenta Azure Migrate: App Containerization ajuda-o a -

- **Descubra a sua aplicação**: A ferramenta conecta-se remotamente aos servidores de aplicação que executam a sua aplicação ASP.NET e descobre os componentes da aplicação. A ferramenta cria um Dockerfile que pode ser usado para criar uma imagem de recipiente para a aplicação.
- **Construa a imagem do recipiente**: Pode inspecionar e personalizar ainda mais o Dockerfile de acordo com os requisitos de aplicação e usá-lo para construir a imagem do seu recipiente de aplicação. A imagem do recipiente de aplicação é empurrada para um registo de contentores Azure que especifique.
- **Implementar para o Serviço Azure Kubernetes**: A ferramenta gera então a definição de recursos de Kubernetes ficheiros YAML necessários para implantar a aplicação contentorizada no seu cluster de Serviço Azure Kubernetes. Pode personalizar os ficheiros YAML e usá-los para implementar a aplicação em AKS.

> [!NOTE]
> A ferramenta Azure Migrate: App Containerization ajuda-o a descobrir tipos específicos de aplicações (aplicações web ASP.NET e Java em Apache Tomcat) e os seus componentes num servidor de aplicações. Para descobrir servidores e o inventário de apps, funções e funcionalidades em execução em máquinas no local, utilize a capacidade de descoberta e avaliação do Azure Migrate: Discovery e assessment. [Saiba mais](./tutorial-discover-vmware.md)

Embora todas as aplicações não beneficiem de uma mudança direta para contentores sem rearquitecção significativa, alguns dos benefícios de mover aplicações existentes para contentores sem reescrever incluem:

- **Melhor utilização das infraestruturas:** Com contentores, várias aplicações podem partilhar recursos e ser hospedados na mesma infraestrutura. Isto pode ajudá-lo a consolidar a infraestrutura e a melhorar a utilização.
- **Gestão simplificada:** Ao hospedar as suas aplicações numa plataforma de infraestrutura gerida moderna como a AKS, pode simplificar as suas práticas de gestão, mantendo ainda o controlo sobre a sua infraestrutura. Você pode conseguir isso aposentando-se ou reduzindo os processos de manutenção e gestão de infraestruturas que você tradicionalmente realizaria com infraestruturas próprias.
- **Portabilidade da aplicação:** Com o aumento da adoção e normalização dos formatos de especificação de contentores e das plataformas de orquestração, a portabilidade da aplicação deixou de ser uma preocupação.
- **Adotar uma gestão moderna com DevOps:** Ajuda-o a adotar e a uniformizar práticas modernas de gestão e segurança com infraestruturas como Código e transição para DevOps.


Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Crie uma conta Azure.
> * Instale a ferramenta Azure Migrate: App Containerization.
> * Descubra a sua aplicação ASP.NET.
> * Construa a imagem do recipiente.
> * Implementar a aplicação contentorizada na AKS.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

**Requisito** | **Detalhes**
--- | ---
**Identifique uma máquina para instalar a ferramenta** | Uma máquina Windows para instalar e executar a ferramenta Azure Migrate: App Containerization. A máquina Do Windows pode ser um sistema operativo de servidor (Windows Server 2016 ou posterior) ou cliente (Windows 10), o que significa que a ferramenta também pode funcionar no seu ambiente de trabalho. <br/><br/> A máquina Windows que executa a ferramenta deve ter conectividade de rede para os servidores/máquinas virtuais que hospedam as aplicações ASP.NET a serem contentorizadas.<br/><br/> Certifique-se de que o espaço de 6-GB está disponível na máquina Windows que executa a ferramenta Azure Migrate: App Containerization para armazenar artefactos de aplicações. <br/><br/> A máquina Windows deve ter acesso à Internet, diretamente ou através de um representante. <br/> <br/>Instale a ferramenta Microsoft Web Deploy na máquina que executa a ferramenta de ajuda de contentores de aplicação e servidor de aplicações se ainda não estiver instalada. Você pode baixar a ferramenta a partir [daqui](https://aka.ms/webdeploy3.6)
**Servidores de aplicações** | Ativar a recolha do PowerShell nos servidores de aplicações: Iniciar sessão no servidor de aplicações e seguir [estas](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting) instruções para ligar o remoting PowerShell. <br/><br/> Se o servidor de aplicações estiver a executar o Windows Server 2008 R2, certifique-se de que o PowerShell 5.1 está instalado no servidor de aplicações. Siga as instruções [aqui](https://docs.microsoft.com/powershell/scripting/windows-powershell/wmf/setup/install-configure) para descarregar e instalar o PowerShell 5.1 no servidor de aplicações. <br/><br/> Instale a ferramenta Microsoft Web Deploy na máquina que executa a ferramenta de ajuda de contentores de aplicação e servidor de aplicações se ainda não estiver instalada. Você pode baixar a ferramenta a partir [daqui](https://aka.ms/webdeploy3.6)
**ASP.NET aplicação** | A ferramenta suporta atualmente <br/><br/> - ASP.NET aplicações utilizando o quadro Microsoft .NET 3.5 ou posterior.<br/> - Servidores de aplicações com o Windows Server 2008 R2 ou posteriormente (os servidores de aplicações devem estar a executar a versão 5.1 do PowerShell). <br/> - Aplicações em execução nos Serviços de Informação da Internet (IIS) 7.5 ou posterior. <br/><br/> A ferramenta atualmente não suporta <br/><br/> - Aplicações que requerem autenticação do Windows (a AKS não suporta gMSA atualmente). <br/> - Aplicações que dependem de outros serviços Windows alojados fora do IIS.


## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de utilizador Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

Uma vez configurada a sua subscrição, necessitará de uma conta de utilizador Azure com:
- Permissões do proprietário na subscrição do Azure
- Permissões para registar aplicações do Azure Ative Directory

Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição. Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir as permissões da seguinte forma:

1. No portal Azure, procure por "subscrições", e em **Serviços,** **selecione Subscrições**.

    ![Caixa de pesquisa para procurar a subscrição do Azure.](./media/tutorial-discover-vmware/search-subscription.png)

2. Na página **Subscrições,** selecione a subscrição na qual pretende criar um projeto Azure Migrate.
3. Na subscrição, selecione **Access Control (IAM)**  >  **Verifique o acesso**.
4. No **Acesso ao Cheques,** procure na conta de utilizador relevante.
5. In **Add a role assignment**, clique em **Adicionar**.

    ![Procure uma conta de utilizador para verificar o acesso e atribuir uma função.](./media/tutorial-discover-vmware/azure-account-access.png)

6. Na **atribuição de funções Add**, selecione a função Proprietário e selecione a conta (azmigrateuser no nosso exemplo). Em seguida, clique em **Guardar**.

    ![Abre a página de atribuição de Função Adicionar para atribuir uma função à conta.](./media/tutorial-discover-vmware/assign-role.png)

7. A sua conta Azure também precisa de **permissões para registar aplicações do Azure Ative Directory.**
8. No portal Azure, navegue para as Definições de Utilizador de Utilizadores **do Diretório Ativo Azure**  >    >  .
9. Nas **definições do Utilizador,** verifique se os utilizadores de Ad Azure podem registar aplicações (definidas para **Sim** por predefinição).

      ![Verifique nas Definições do Utilizador que os utilizadores podem registar aplicações ative directory.](./media/tutorial-discover-vmware/register-apps.png)

10. Caso as definições de 'Registos de aplicações' sejam definidas como 'Não', solicite ao arrendatário/administrador global que atribua a permissão necessária. Em alternativa, o administrador inquilino/global pode atribuir o papel **de Desenvolvedor de Aplicações** a uma conta para permitir o registo da App Azure Ative Directory App. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).


## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Descarregue e instale a Azure Migrate: Ferramenta de contentorização de aplicativos

1. [Descarregue](https://go.microsoft.com/fwlink/?linkid=2134571) o instalador Azure Migrate: App Containerization numa máquina Windows.
2. Lançar PowerShell no modo de administrador e alterar o diretório PowerShell para a pasta que contém o instalador.
3. Executar o script de instalação usando o comando

   ```powershell
   .\App ContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>Lançar a ferramenta de contentorização de aplicativos

1. Abra um browser em qualquer máquina que possa ligar à máquina do Windows que executa a ferramenta de Contentorização de Aplicações e abra o URL da ferramenta: **https:// nome da máquina ou endereço *IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho selecionando o atalho da aplicação.

2. Se vir um aviso indicando que a sua ligação não é privada, clique em Advanced e opte por seguir para o site. Este aviso aparece à medida que a interface web utiliza um certificado TLS/SSL auto-assinado.
3. No ecrã de inscrição, utilize a conta de administrador local na máquina para iniciar scontabilidade.
4. Para especificar o tipo de aplicação, selecione **ASP.NET aplicações web** como o tipo de aplicação que pretende contentorizar.

    ![Carga padrão para a ferramenta de contentorização de aplicativos.](./media/tutorial-containerize-apps-aks/tool-home.png)


### <a name="complete-tool-pre-requisites"></a>Pré-requisitos completos da ferramenta
1. Aceite os termos da **licença** e leia as informações de terceiros.
6. Na aplicação web de ferramentas > **Configurar pré-requisitos,** faça os seguintes passos:
   - **Conectividade**: A ferramenta verifica se a máquina do Windows tem acesso à Internet. Se a máquina utilizar um representante:
     - Clique em **Configurar o proxy** para especificar o endereço de procuração (no endereço IP do formulário ou FQDN) e na porta de audição.
     - Especifique as credenciais se o proxy precisar de autenticação.
     - Apenas é suportado o proxy HTTP.
     - Se tiver adicionado detalhes de procuração ou desativado o proxy e/ou autenticação, clique em **Guardar** para ativar novamente a verificação de conectividade.
   - **Instale atualizações**: A ferramenta verificará automaticamente as atualizações mais recentes e instalá-las-á. Também pode instalar manualmente a versão mais recente da ferramenta a partir [daqui.](https://go.microsoft.com/fwlink/?linkid=2134571)
   - **Instale a ferramenta Microsoft Web Deploy**: A ferramenta verificará se a ferramenta Microsoft Web Deploy está instalada na máquina Windows que executa a ferramenta Azure Migrate: App Containerization.
   - **Ativar o remoing powerShell**: A ferramenta irá informá-lo para garantir que o remoing PowerShell está ativado nos servidores de aplicações que executam as aplicações ASP.NET a serem contentorizadas.


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Clique em **Iniciar sessão** para iniciar sessão na sua conta Azure.

1. Vai precisar de um código de dispositivo para autenticar com o Azure. Clicar no Login abrirá um modal com o código do dispositivo.
2. Clique no **código copy & Iniciar sessão** para copiar o código do dispositivo e abrir um pedido de Login Azure num novo separador de navegador. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.

    ![Código do dispositivo de exibição modal.](./media/tutorial-containerize-apps-aks/login-modal.png)

3. No novo separador, cole o código do dispositivo e faça login completo utilizando as credenciais da sua conta Azure. Pode fechar o separador do navegador depois de o início de sessão estar completo e regressar à interface web da ferramenta de Contentorização de Aplicações.
4. Selecione o **inquilino Azure** que pretende usar.
5. Especifique a **subscrição Azure** que pretende utilizar.

## <a name="discover-aspnet-applications"></a>Descubra aplicações ASP.NET

A ferramenta de ajuda de contentores de aplicações conecta-se remotamente aos servidores de aplicações utilizando as credenciais fornecidas e tenta descobrir ASP.NET aplicações hospedadas nos servidores de aplicações.

1. Especifique o **endereço IP/FQDN e as credenciais** do servidor que executa a aplicação ASP.NET que deve ser usada para ligar remotamente ao servidor para a descoberta da aplicação.
    - As credenciais fornecidas devem ser para um administrador local (Windows) no servidor de aplicações.
    - Para contas de domínio (o utilizador deve ser um administrador no servidor de aplicações), prefixe o nome de utilizador com o nome de domínio no formato *<domínio\username>*.
    - Pode executar a descoberta de aplicações para até cinco servidores de cada vez.

2. Clique **em Validar** para verificar se o servidor de aplicação está acessível a partir da máquina que executa a ferramenta e que as credenciais são válidas. Após uma validação bem sucedida, a coluna de estado mostrará o estado como **Mapeado**.  

    ![Screenshot para o servidor IP e credenciais.](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. Clique **em Continuar** a iniciar a descoberta da aplicação nos servidores de aplicações selecionados.

4. Após a conclusão com sucesso da descoberta da aplicação, pode selecionar a lista de aplicações para contentorizar.

    ![Screenshot para ASP.NET aplicação descoberta.](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. Utilize a caixa de verificação para selecionar as aplicações para o contentor.
5. **Especificar o nome do recipiente**: Especifique o nome do recipiente-alvo para cada aplicação selecionada. O nome do recipiente deve ser especificado como <*nome:>* onde a etiqueta é utilizada para a imagem do recipiente. Por exemplo, pode especificar o nome do recipiente-alvo como *appname:v1*.   

### <a name="parameterize-application-configurations"></a>Parametrizar configurações de aplicações
Parametrizar a configuração torna-a disponível como parâmetro de tempo de implantação. Isto permite-lhe configurar esta definição enquanto implementa a aplicação em vez de a ter codificada duramente para um valor específico na imagem do recipiente. Por exemplo, esta opção é útil para parâmetros como cadeias de ligação de base de dados.
1. Clique nas **configurações da aplicação** para rever as configurações detetadas.
2. Selecione a caixa de verificação para parametrizar as configurações detetadas da aplicação.
3. Clique **em Aplicar** depois de selecionar as configurações para parametrizar.

   ![Screenshot para a configuração da aplicação ASP.NET aplicação.](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>Externalizar as dependências do sistema de ficheiros

 Pode adicionar outras pastas que a sua aplicação utiliza. Especificar se devem fazer parte da imagem do recipiente ou devem ser externalizados através de volumes persistentes na partilha de ficheiros Azure. A utilização de volumes persistentes funciona muito bem para aplicações imponentes que armazenam o estado fora do contentor ou têm outros conteúdos estáticos armazenados no sistema de ficheiros. [Saiba mais](https://docs.microsoft.com/azure/aks/concepts-storage)

1. Clique em **Editar** em Pastas de Aplicação para rever as pastas de aplicação detetadas. As pastas de aplicação detetadas foram identificadas como artefactos obrigatórios necessários pela aplicação e serão copiadas para a imagem do contentor.

2. Clique **em Adicionar pastas** e especifique os caminhos das pastas a adicionar.
3. Para adicionar várias pastas ao mesmo volume, forneça `,` vírgulas separadas.
4. Selecione **o Volume Persistente** como opção de armazenamento se quiser que as pastas sejam armazenadas fora do recipiente num Volume Persistente.
5. Clique **em Guardar** depois de rever as pastas de aplicação.
   ![Screenshot para seleção de armazenamento de volumes de aplicativos.](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. Clique **Em Continuar** a avançar para a fase de construção da imagem do recipiente.

## <a name="build-container-image"></a>Criar imagem de contentor


1. **Selecione registo do contentor Azure**: Utilize o dropdown para selecionar um [Registo de Contentores Azure](https://docs.microsoft.com/azure/container-registry/) que será utilizado para construir e armazenar as imagens do contentor para as aplicações. Pode utilizar um registo de contentores Azure existente ou optar por criar um novo utilizando a nova opção de registo.

    ![Screenshot para a seleção de aplicativo ACR.](./media/tutorial-containerize-apps-aks/build-aspnet-app.png)


2. **Reveja o Dockerfile**: O Dockerfile necessário para construir as imagens de contentores para cada aplicação selecionada é gerado no início do passo de construção. Clique **em Rever** para rever o Dockerfile. Também pode adicionar quaisquer personalizações necessárias ao Dockerfile na etapa de revisão e guardar as alterações antes de iniciar o processo de construção.

3. **Processo de construção do gatilho**: Selecione as aplicações para construir imagens e clique em **Construir**. Clicar na construção iniciará a construção da imagem do recipiente para cada aplicação. A ferramenta continua a monitorizar continuamente o estado de construção e permitir-lhe-á avançar para o próximo passo após a conclusão com sucesso da construção.

4. **Estado de construção da faixa**: Também pode monitorizar o progresso do passo de construção clicando na ligação **Build in Progress** sob a coluna status. A ligação leva alguns minutos para estar ativo depois de ter desencadeado o processo de construção.  

5. Uma vez concluída a construção, clique **em Continuar** a especificar as definições de implementação.

    ![Screenshot para a conclusão da imagem do recipiente de aplicações.](./media/tutorial-containerize-apps-aks/build-aspnet-app-completed.png)

## <a name="deploy-the-containerized-app-on-aks"></a>Implementar a aplicação contentorizada em AKS

Uma vez construída a imagem do contentor, o próximo passo é implantar a aplicação como recipiente no [Serviço Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/).

1. **Selecione o Cluster de ServiçoS Azure Kubernetes**: Especifique o cluster AKS para o qual a aplicação deve ser implantada.

     - O cluster AKS selecionado deve ter uma piscina de nó windows.
     - O cluster deve ser configurado para permitir a retirada de imagens do Registo do Contentor Azure que foi selecionado para armazenar as imagens.
         - Executar o seguinte comando em Azure CLI para ligar o cluster AKS ao ACR.
           ``` Azure CLI
           az aks update -n <cluster-name> -g <cluster-resource-group> --attach-acr <acr-name>
           ```  
     - Se não tiver um cluster AKS ou quiser criar um novo cluster AKS para implementar a aplicação, pode optar por criar a partir da ferramenta clicando em **Criar um novo cluster AKS.**      
          - O cluster AKS criado com a ferramenta será criado com um conjunto de nós Windows. O cluster será configurado para permitir que ele retire imagens do Registo do Contentor de Azure que foi criado anteriormente (se for escolhida nova opção de registo).
     - Clique **em Continuar** depois de selecionar o cluster AKS.

2. **Especificar a partilha de ficheiros Azure**: Se tiver adicionado mais pastas e selecionado a opção Volume Persistente, especifique a partilha de ficheiros Azure que deve ser utilizada pela Azure Migrate: Ferramenta de contentorização de aplicações durante o processo de implantação. A ferramenta criará novos diretórios nesta partilha de ficheiros Azure para copiar sobre as pastas de aplicação configuradas para armazenamento de Volume Persistente. Uma vez concluída a implementação da aplicação, a ferramenta limpará a parte do ficheiro Azure eliminando os diretórios que tinha criado.

     - Se não tiver uma partilha de ficheiros Azure ou quiser criar uma nova partilha de ficheiros Azure, pode optar por criar a partir da ferramenta clicando em **Criar uma nova Conta de Armazenamento e partilha de ficheiros.**  

3. **Configuração da implementação** da aplicação : Uma vez concluídas as etapas acima, terá de especificar a configuração de implementação da aplicação. Clique **em Configurar** para personalizar a implementação para a aplicação. No passo de configuração pode fornecer as seguintes personalizações:
     - **Cadeia de prefixo**: Especifique uma cadeia de prefixo para usar no nome de todos os recursos criados para a aplicação contentorizada no cluster AKS.
     - **Certificado SSL**: Se a sua aplicação necessitar de uma ligação ao site https, especifique o ficheiro PFX que contém o certificado a ser utilizado para a encadernação. O ficheiro PFX não deve ser protegido por palavra-passe e o site original não deve ter múltiplas ligações.
     - **Conjuntos de réplicas**: Especifique o número de instâncias de aplicação (cápsulas) que devem ser executadas dentro dos contentores.
     - **Tipo de balançador de** carga : Selecione *Externo* se a aplicação contentorizada deve ser acessível a partir de redes públicas.
     - **Configuração da aplicação**: Para quaisquer configurações de aplicação que foram parametrizadas, forneça os valores a utilizar para a implementação atual.
     - **Armazenamento**: Para quaisquer pastas de aplicação configuradas para armazenamento de volume persistente, especifique se o volume deve ser partilhado em instâncias de aplicação ou deve ser inicializado individualmente com cada instância no recipiente. Por predefinição, todas as pastas de aplicação em Volumes Persistentes são configuradas como partilhadas.  
     - Clique **em Aplicar** para guardar a configuração de implementação.
     - Clique **Em Continuar** a implementar a aplicação.

    ![Screenshot para configuração de aplicação de implementação.](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-config.png)

4. **Implementar a aplicação**: Uma vez guardada a configuração de implementação da aplicação, a ferramenta gerará a implementação de Kubernetes YAML para a aplicação.
     - Clique **em Editar** para rever e personalizar a implementação de Kubernetes YAML para as aplicações.
     - Selecione a aplicação para implementar.
     - Clique **em Implementar** para iniciar implementações para as aplicações selecionadas

         ![Screenshot para configuração de implementação de aplicações.](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-deploy.png)

     - Uma vez implementada a aplicação, pode clicar na coluna *de estado de Implementação* para rastrear os recursos que foram implantados para a aplicação.

## <a name="download-generated-artifacts"></a>Descarregar artefactos gerados

Todos os artefactos utilizados para construir e implantar a aplicação em AKS, incluindo os ficheiros de especificações YAML do Dockerfile e kubernetes, são armazenados na máquina que executa a ferramenta. Os artefactos estão localizados em *C:\ProgramData\Microsoft Azure Migrate App Containerization*.

Uma única pasta é criada para cada servidor de aplicações. Pode visualizar e baixar todos os artefactos intermédios utilizados no processo de contentorização navegando para esta pasta. A pasta, correspondente ao servidor de aplicações, será limpa no início de cada execução da ferramenta para um determinado servidor.

## <a name="troubleshoot-issues"></a>Resolver problemas

Para resolver problemas com a ferramenta, pode consultar os ficheiros de registo da máquina Windows que executa a ferramenta de Contentorização de Aplicações. Os ficheiros de registo de ferramentas estão localizados na pasta *C:\ProgramData\Microsoft Azure Migrate App Containerization\Logs.*

## <a name="next-steps"></a>Passos seguintes

- Contentorizando as Aplicações Web java em Apache Tomcat (nos servidores Linux) e implantando-as em recipientes Linux em AKS. [Saiba mais](./tutorial-containerize-java-kubernetes.md)
