---
title: Agente de compilação de utilização do Azure Container Instances, como um Jenkins
description: Saiba como utilizar o Azure Container Instances, tal como agente de compilação de um Jenkins.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/31/2018
ms.author: danlep
ms.openlocfilehash: 2687a64bfd952888086862d929a3e5869aee5a4f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58003430"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Agente de compilação de utilização do Azure Container Instances, como um Jenkins

O Azure Container Instances (ACI) fornece um ambiente a pedido, burstable e isolado para executar cargas de trabalho em contentores. Devido a esses atributos, o ACI faz uma excelente plataforma para executar tarefas de compilação do Jenkins em grande escala. Este artigo explica-implementação e utilização de um servidor do Jenkins que é pré-configurado com ACI como um destino de compilação.

Para obter mais informações sobre o Azure Container Instances, consulte [sobre o Azure Container Instances][about-aci].

## <a name="deploy-a-jenkins-server"></a>Implementar um servidor Jenkins

1. No portal do Azure, selecione **criar um recurso** e procure **Jenkins**. Selecione a oferta de Jenkins com um editor de **Microsoft**e, em seguida, selecione **criar**.

2. Introduza as seguintes informações o **Noções básicas** formam e, em seguida, selecione **OK**.

   - **Nome**: Introduza um nome para a implementação do Jenkins.
   - **Nome de utilizador**: Introduza um nome para o utilizador de administrador da máquina virtual Jenkins.
   - **Tipo de autenticação**: Recomendamos que uma chave pública de SSH para autenticação. Se selecionar esta opção, cole uma chave pública de SSH a ser utilizado para iniciar sessão para a máquina virtual do Jenkins.
   - **Subscrição**: Selecione uma subscrição do Azure.
   - **Grupo de recursos**: Crie um grupo de recursos ou selecione um existente.
   - **Localização**: Selecione uma localização para o servidor Jenkins.

   ![Definições básicas para a implementação de portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Sobre o **definições adicionais** de formulário, conclua os seguintes itens:

   - **Tamanho**: Selecione a opção de dimensionamento apropriado para a máquina virtual do Jenkins.
   - **Tipo de disco VM**: Especifique **HDD** (unidade de disco rígido) ou **SSD** (unidade de estado sólido) para o servidor Jenkins.
   - **Rede virtual**: Se pretender modificar as predefinições, selecione a seta.
   - **Sub-redes**: Selecione a seta, verifique as informações e selecione **OK**.
   - **Endereço IP público**: Selecione a seta para fornecer o endereço IP público um nome personalizado, configure o SKU e definir o método de atribuição.
   - **Etiqueta de nome de domínio**: Especifique um valor para criar um URL completamente qualificado para a máquina virtual do Jenkins.
   - **Tipo de versão do Jenkins**: Selecione o tipo de versão desejada entre as opções: **LTS**, **semanalmente crie**, ou **Azure verificado**.

   ![Definições adicionais para a implementação de portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Para a integração de principal de serviço, selecione **Auto(MSI)** ter [geridos identidades para recursos do Azure] [ managed-identities-azure-resources] criar automaticamente uma identidade de autenticação para o Instância do Jenkins. Selecione **Manual** para fornecer suas próprias credenciais de principal de serviço.

5. Agentes de cloud configurar uma plataforma com base na cloud, para tarefas de compilação do Jenkins. Neste artigo, selecione **ACI**. Com o agente de cloud ACI, cada tarefa de compilação do Jenkins é executada numa instância de contentor.

   ![Definições de integração na cloud para a implementação de portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Quando tiver terminado com as definições de integração, selecione **OK**e, em seguida, selecione **OK** novamente na validação de resumida. Selecione **Create** sobre o **termos de utilização** resumo. O servidor Jenkins demora alguns minutos a implementar.

## <a name="configure-jenkins"></a>Configurar o Jenkins

1. No portal do Azure, navegue para o grupo de recursos do Jenkins, selecione a máquina virtual do Jenkins e tome nota do nome DNS.

   ![Nome DNS em detalhes sobre a máquina virtual do Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Navegue para o nome DNS da VM do Jenkins e copie a cadeia de caracteres retornada do SSH.

   ![Instruções de início de sessão do Jenkins pela cadeia SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Abra uma sessão de terminal no sistema de desenvolvimento e cole a cadeia de SSH do último passo. Atualização `username` para o nome de utilizador que especificou ao implementar o servidor Jenkins.

4. Após a sessão está ligada, execute o seguinte comando para obter a palavra-passe de administrador inicial:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Deixe a sessão SSH e túnel em execução e ir para `http://localhost:8080` num browser. Cole a palavra-passe de administrador inicial na caixa e, em seguida, selecione **continuar**.

   ![Ecrã "Desbloquear o Jenkins" com a caixa da palavra-passe de administrador](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Selecione **instale os plugins sugeridos** instalar todos os recomendado plug-ins do Jenkins.

   ![Ecrã "Personalizar Jenkins" com "Instale os plugins sugeridos" selecionado](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Crie uma conta de utilizador de administrador. Esta conta é utilizada para iniciar sessão e trabalhar com a instância do Jenkins.

   ![Ecrã "Criar o primeiro utilizador administrador", com credenciais preenchidos](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Selecione **guardar e concluir**e, em seguida, selecione **começar a utilizar o Jenkins** para concluir a configuração.

Jenkins está agora configurado e pronto para criar e implementar código. Neste exemplo, uma aplicação Java simples é utilizada para demonstrar uma compilação Jenkins no Azure Container Instances.

## <a name="create-a-build-job"></a>Criar uma tarefa de compilação

Agora, é criada uma tarefa de compilação do Jenkins para demonstrar a que jenkins baseia-se uma instância de contentor do Azure.

1. Selecione **Novo Item**, dê um nome de projeto de compilação como **aci-demo**, selecione **projeto Freestyle**e selecione **OK**.

   ![Caixa para o nome da tarefa de compilação e lista de tipos de projeto](./media/container-instances-jenkins/jenkins-new-job.png)

2. Sob **gerais**, certifique-se de que **restringir onde pode ser executado neste projeto** está selecionada. Introduza **linux** para a expressão de rótulo. Esta configuração garante que esta tarefa de compilação é executada na ACI cloud.

   ![Separador "Geral", com detalhes de configuração](./media/container-instances-jenkins/jenkins-job-01.png)

3. Sob **crie**, selecione **Adicionar passo de compilação** e selecione **executar Shell**. Introduza `echo "aci-demo"` como o comando.

   ![Separador "Build" com as seleções para a etapa de compilação](./media/container-instances-jenkins/jenkins-job-02.png)

5. Selecione **Guardar**.

## <a name="run-the-build-job"></a>Executar a tarefa de compilação

Para testar a tarefa de compilação e observe como a plataforma de compilação do Azure Container Instances, inicie manualmente uma compilação.

1. Selecione **compilar agora** para iniciar uma tarefa de compilação. Demora alguns minutos para que a tarefa iniciar. Deverá ver um status que é semelhante à seguinte imagem:

   ![Informações de "Histórico de compilações" com o estado da tarefa](./media/container-instances-jenkins/jenkins-job-status.png)

2. Enquanto a tarefa está em execução, abra o portal do Azure e ver o grupo de recursos do Jenkins. Deverá ver que foi criada uma instância de contentor. A tarefa do Jenkins está em execução dentro desta instância.

   ![Instância de contentor no grupo de recursos](./media/container-instances-jenkins/jenkins-aci.png)

3. Como o Jenkins é executado mais tarefas do que o número configurado de executores do Jenkins (predefinição de 2), são criadas várias instâncias de contentor.

   ![Instâncias de contentor criada de novo](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Depois de terminar todas as tarefas de compilação, as instâncias de contentores são removidas.

   ![Grupo de recursos com o container instances removido](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Resolver problemas nos plug-ins do Jenkins

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais acerca do Jenkins no Azure, veja [do Azure e o Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-identities-azure-resources]: ../active-directory/managed-identities-azure-resources/overview.md