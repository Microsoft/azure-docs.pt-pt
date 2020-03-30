---
title: Jenkins constrói em instância de contentores
description: Aprenda a configurar um servidor Jenkins para executar trabalhos a pedido em Casos de Contentores Azure
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: 8bb84895fb581053248fbad326ea7b2c8d1873a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617973"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Use as instâncias de contentores Azure como um agente de construção Jenkins

As instâncias de contentores Azure (ACI) proporcionam um ambiente a pedido, desfasável e isolado para o funcionamento de cargas de trabalho contentorizadas. Devido a estes atributos, a ACI faz uma grande plataforma para gerir a Jenkins construir empregos em larga escala. Este artigo passa pela implementação e utilização de um servidor Jenkins que está pré-configurado com ACI como um alvo de construção.

Para obter mais informações sobre as instâncias de contentores do Azure, consulte as instâncias de [contentores azure](../container-instances/container-instances-overview.md).

## <a name="deploy-a-jenkins-server"></a>Implementar um servidor Jenkins

1. No portal Azure, selecione **Criar um recurso** e procurar **jenkins.** Selecione a oferta do Jenkins com um editor da **Microsoft**, e depois selecione **Criar**.

2. Introduza as seguintes informações no formulário **Basics** e, em seguida, selecione **OK**.

   - **Nome**: Introduza um nome para a implantação de Jenkins.
   - **Nome do utilizador**: Introduza um nome para o utilizador administrativo da máquina virtual Jenkins.
   - Tipo de **autenticação**: Recomendamos uma chave pública SSH para autenticação. Se selecionar esta opção, colhe numa chave pública SSH para ser utilizada para iniciar sessão na máquina virtual Jenkins.
   - **Subscrição**: Selecione uma subscrição Azure.
   - **Grupo de recursos**: crie um grupo de recursos ou selecione um grupo existente.
   - **Localização**: Selecione uma localização para o servidor Jenkins.

   ![Definições básicas para a implementação do portal Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

3. No formulário **Definições Adicionais,** preencha os seguintes itens:

   - **Tamanho**: Selecione a opção de tamanho apropriado para a sua máquina virtual Jenkins.
   - Tipo de **disco VM:** Especifique o **HDD** (disco rígido) ou o **SSD** (unidade de estado sólido) para o servidor Jenkins.
   - **Rede virtual**: Selecione a seta se pretender modificar as definições predefinidas.
   - **Sub-redes**: Selecione a seta, verifique as informações e selecione **OK**.
   - **Endereço IP público**: Selecione a seta para dar ao endereço IP público um nome personalizado, configure o SKU e detetete o método de atribuição.
   - **Etiqueta de nome de domínio**: Especifique um valor para criar um URL totalmente qualificado para a máquina virtual Jenkins.
   - **Tipo**de lançamento jenkins : Selecione o tipo de libertação desejado a partir das opções: **LTS,** **Construção Semanal**ou **Verificado Azure**.

   ![Configurações adicionais para a implementação do portal Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Para a integração principal do serviço, selecione **Auto(MSI)** para ter [gerido identidades para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) automaticamente criar uma identidade de autenticação para a instância Jenkins. Selecione **Manual** para fornecer as suas credenciais principais de serviço.

5. Os agentes da nuvem configuram uma plataforma baseada na nuvem para o Jenkins construir empregos. Para o bem deste artigo, selecione **ACI**. Com o agente de nuvem da ACI, cada trabalho de construção de Jenkins é executado numa instância de contentores.

   ![Definições de integração em nuvem para implementação do portal Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Quando terminar as definições de integração, selecione **OK**, e, em seguida, selecione **OK** novamente no resumo da validação. Selecione **Criar** no resumo **de termos de utilização.** O servidor Jenkins leva alguns minutos a ser implantado.

## <a name="configure-jenkins"></a>Configurar o Jenkins

1. No portal Azure, navegue pelo grupo de recursos Jenkins, selecione a máquina virtual Jenkins e tome nota do nome DNS.

   ![Nome DNS em detalhes sobre a máquina virtual Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Navegue pelo nome DNS do Jenkins VM e copie a cadeia SSH devolvida.

   ![Jenkins instruções de login com corda SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Abra uma sessão terminal no seu sistema de desenvolvimento e cola na corda SSH a partir do último passo. Atualize `username` o nome de utilizador que especificou quando implementou o servidor Jenkins.

4. Depois de a sessão estar ligada, execute o seguinte comando para recuperar a senha inicial de administração:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Deixe a sessão sHH e `http://localhost:8080` o túnel em funcionamento, e vá para um navegador. Passe a palavra-passe inicial na caixa e, em seguida, selecione **Continuar**.

   ![Ecrã "Desbloquear Jenkins" com a caixa para a senha do administrador](./media/container-instances-jenkins/jenkins-portal-05.png)

6. **Selecione Instale plugins sugeridos** para instalar todos os plugins Recomendados jenkins.

   ![Ecrã "Personalizar Jenkins" com "Instalar plugins sugeridos" selecionados](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Crie uma conta de utilizador de administração. Esta conta é usada para iniciar sessão e trabalhar com a sua instância Jenkins.

   ![Ecrã "Create First Admin User", com credenciais preenchidas](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Selecione **Guardar e Terminar**e, em seguida, selecione Começar a utilizar o **Jenkins** para completar a configuração.

Jenkins está agora configurado e pronto para construir e implementar código. Para este exemplo, uma aplicação java simples é usada para demonstrar uma construção jenkins em casos de contentores Azure.

## <a name="create-a-build-job"></a>Criar um trabalho de construção

Agora, um trabalho de construção jenkins é criado para demonstrar que Jenkins constrói um caso de contentor Esazulado.

1. Selecione **Novo Item,** dê ao projeto de construção um nome como **aci-demo,** selecione **projeto Freestyle,** e selecione **OK**.

   ![Caixa para o nome do trabalho de construção, e lista de tipos de projeto](./media/container-instances-jenkins/jenkins-new-job.png)

2. Em **geral,** certifique-se de que **a Restrição onde este projeto pode ser executado** é selecionada. Introduza **o linux** para a expressão do rótulo. Esta configuração garante que este trabalho de construção funciona na nuvem ACI.

   ![Separador "Geral" com detalhes de configuração](./media/container-instances-jenkins/jenkins-job-01.png)

3. Em **'Build',** **selecione Adicionar passo de construção** e selecione Executar **shell**. Entra `echo "aci-demo"` como comando.

   ![Separador "Construir" com seleções para o passo de construção](./media/container-instances-jenkins/jenkins-job-02.png)

5. Selecione **Guardar**.

## <a name="run-the-build-job"></a>Executar o trabalho de construção

Para testar o trabalho de construção e observar as Instâncias de Contentores Azure como plataforma de construção, inicie manualmente uma construção.

1. Selecione **Build Now** para iniciar um trabalho de construção. Leva alguns minutos para o trabalho começar. Deve ver um estado semelhante à seguinte imagem:

   ![Informação "Construir História" com estatuto profissional](./media/container-instances-jenkins/jenkins-job-status.png)

2. Enquanto o trabalho está em execução, abra o portal Azure e olhe para o grupo de recursos Jenkins. Devia ver que foi criada uma instância de contentores. O trabalho do Jenkins está a correr para dentro desta instância.

   ![Instância de contentores no grupo de recursos](./media/container-instances-jenkins/jenkins-aci.png)

3. À medida que jenkins gere mais empregos do que o número configurado de executores Jenkins (padrão 2), são criadas várias instâncias de contentores.

   ![Casos de contentores recém-criados](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Depois de todos os trabalhos de construção terem terminado, as instâncias dos contentores são removidas.

   ![Grupo de recursos com instâncias de contentores removidos](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Resolver problemas nos plug-ins do Jenkins

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [CI/CD to Azure App Service](java-deploy-webapp-tutorial.md) (CI/CD no Serviço de Aplicações do Azure)