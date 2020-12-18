---
title: Tutorial para exportar dados da Caixa de Dados do Azure Microsoft Docs
description: Conheça os pré-requisitos de implantação e como exportar dados a partir de uma Caixa de Dados Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: e822a2be200f701d65ab2080804d252f99589680
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680788"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>Tutorial: Criar ordem de exportação para Azure Data Box

A Azure Data Box é uma solução híbrida que permite mover dados do Azure para a sua localização. Este tutorial descreve como criar uma ordem de exportação para a Caixa de Dados Azure. A principal razão para criar uma ordem de exportação é a recuperação de catástrofes, caso o armazenamento no local seja comprometido e seja necessário restabelecer um apoio.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos para exportação
> * Encomende uma Caixa de Dados para exportação
> * Acompanhe a ordem de exportação
> * Cancelar a ordem de exportação

## <a name="prerequisites"></a>Pré-requisitos

Preencha os seguintes pré-requisitos de configuração para o serviço de Caixa de Dados e dispositivo antes de encomendar o dispositivo.

### <a name="for-service"></a>Para o serviço

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Certifique-se de que tem um grupo de recursos existente que pode utilizar com a sua Caixa de Dados Azure.

* Certifique-se de que a sua conta de Armazenamento Azure de onde pretende exportar dados é um dos tipos de conta de armazenamento suportados como contas de [armazenamento suportadas descritas para a Caixa](data-box-system-requirements.md#supported-storage-accounts)de Dados .

### <a name="for-device"></a>Para o dispositivo

Antes de começar, certifique-se de que:

* Deve ter um computador anfitrião ligado à rede do datacenter. Copiará os dados da Caixa de Dados Azure para este computador. O computador anfitrião tem de executar um sistema operativo suportado, conforme descrito em [Requisitos de sistema do Azure Data Box](data-box-system-requirements.md).

* O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se não estiver disponível uma ligação de 10 GbE, pode ser utilizada uma ligação de dados de 1 GbE, mas as velocidades de cópia são impactadas.

## <a name="order-data-box-for-export"></a>Caixa de dados de encomenda para exportação

Execute os passos seguintes no Portal do Azure para encomendar um dispositivo.

1. Utilize as suas credenciais do Microsoft Azure para iniciar sessão neste URL: [https://portal.azure.com](https://portal.azure.com).

2. Selecione **+ Criar um recurso** e procure *Azure Data Box*. Selecione **Azure Data Box**.

   ![Criar um recurso](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. Selecione **Criar**.

   ![Criar um recurso Azure Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. Verifique se o serviço Azure Data Box está disponível na sua região. Introduza ou selecione as seguintes informações e selecione **Aplicar**.

    |Definição  |Valor  |
    |---------|---------|
    |Tipo de transferência     | Selecione **Export to Azure**.        |
    |Subscrição     | Selecione uma subscrição EA, CSP ou Azure Sponsorship para o serviço Data Box. <br> A subscrição está ligada à sua conta de faturação.       |
    |Grupo de recursos     |    Selecione um grupo de recursos existente. <br> Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto.         |
    |Fonte Azure    |    Selecione a região Azure onde os seus dados estão atualmente.         |
    |País de destino     |     Selecione o país onde pretende enviar o dispositivo.        |

   ![Selecione as definições da Caixa de Dados](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. Selecione **Data Box**. A capacidade máxima utilizável para uma única encomenda é de 80 TB. Pode criar várias encomendas para tamanhos de dados maiores.

   ![Selecione a capacidade da Caixa de Dados](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. Em **Ordem**, especifique os detalhes da encomenda **básica.** Introduza ou selecione as seguintes informações.

    |Definição  |Valor  |
    |---------|---------|
    |Subscrição     | A subscrição é automaticamente povoada com base na sua seleção anterior.|
    |Grupo de recursos | O grupo de recursos que selecionou anteriormente. |
    |Nome da encomenda de exportação     |  Forneça um nome amigável para controlar a encomenda. <br> O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. <br> O nome tem de começar e terminar com uma letra ou um número.      |

    ![Fundamentos da ordem de exportação](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    Selecione **Seguinte: Data Selection** para prosseguir.

7. Na **Data Selection**, selecione Adicionar a conta de armazenamento e o tipo de **exportação**.

    ![Adicionar conta de armazenamento e tipo de exportação](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. Na **opção Select Export**, especifique os detalhes da opção de exportação. Introduza ou selecione as seguintes informações e **selecione Adicionar**.

    |Definição  |Valor  |
    |---------|---------|
    |Conta de armazenamento     | A conta de Armazenamento Azure de onde pretende exportar dados. |
    |Tipo de exportação     | Especifica o tipo de dados para exportar de **todos os objetos** e **utilizar o ficheiro XML**.<ul><li> **Todos os objetos** - Especifica que o trabalho exporta todos os dados dependendo da sua seleção para **opções de Transferência.**</li><li> **Utilize o ficheiro XML** – Especifica um ficheiro XML que contém um conjunto de caminhos e prefixos para bolhas e/ou ficheiros a serem exportados da conta de armazenamento. O ficheiro XML tem de estar no recipiente da conta de armazenamento selecionada e a seleção das ações de ficheiros não é suportada. O ficheiro tem de ser um ficheiro de .xml não vazio.</li></ul>        |
    |Opções de transferência     |  Especifica as opções de transferência de dados **de Selecione todos**, **Todas as bolhas** e **todos os ficheiros**. <ul><li> **Selecione All** - Especifica que todas as bolhas e ficheiros Azure são exportados. Se estiver a utilizar uma conta de armazenamento que suporta apenas bolhas (Conta de Armazenamento Blob), a opção **Todos os Ficheiros** não será selecionada.</li><li> **Todas as Bolhas** - Especifica que apenas blobs de bloco e página são exportados.</li><li> **Todos os ficheiros** - Especifica que todos os ficheiros são exportados, excluindo bolhas. O tipo de conta de armazenamento que tem (GPv1 e GPv2, armazenamento premium ou armazenamento de bolhas) determina os tipos de dados que pode exportar. Para obter mais informações, consulte [as contas de armazenamento suportadas para exportação.](../storage/common/storage-import-export-requirements.md#supported-storage-types)</li></ul>         |
    |Incluir log verbose     | Indica se deseja um ficheiro de registo verboso que contenha uma lista de todos os ficheiros que foram exportados com sucesso.        |

    > [!NOTE]
    >
    > Se selecionar Utilize o **ficheiro XML** para a definição do **tipo Exportação,** tem de se certificar de que o xml contém caminhos e/ou prefixos válidos. Tem de construir e fornecer o ficheiro XML.  Se o ficheiro for inválido ou nenhum dado corresponder aos caminhos especificados, a encomenda termina com dados parciais ou sem dados exportados.

    Para ver como adicionar um ficheiro XML a um recipiente, consulte [a ordem de exportação utilizando o ficheiro XML](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Selecione opção de exportação](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   Para ver um exemplo da entrada xml, consulte a [entrada Sample XML](data-box-deploy-export-ordered.md#sample-xml-file)

9. Na **seleção de Dados,** reveja as suas definições e selecione **Seguinte:>** de segurança para continuar.

   ![Ordem de exportação, seleção de dados](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    O ecrã **de Segurança** permite-lhe usar a sua própria chave de encriptação e optar por utilizar a encriptação dupla.

    Todas as definições no ecrã **de Segurança** são opcionais. Se não alterar quaisquer definições, aplicar-se-ão as definições predefinidas.

    ![Tela de segurança do assistente de encomenda de importação de caixa de dados](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. Se pretender utilizar a sua própria chave gerida pelo cliente para proteger a chave de desbloqueio para o seu novo recurso, expanda o **tipo de encriptação**.

    Configurar uma chave gerida pelo cliente para a sua Caixa de Dados Azure é opcional. Por predefinição, a Data Box utiliza uma chave gerida pela Microsoft para proteger a chave de desbloqueio.

    Uma chave gerida pelo cliente não afeta a forma como os dados do dispositivo são encriptados. A chave é usada apenas para encriptar a chave de desbloqueio do dispositivo.

    Se não quiser utilizar uma chave gerida pelo cliente, salte para o passo 16.

    ![Ecrã de segurança mostrando definições do tipo de encriptação](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. Selecione **a chave gerida pelo Cliente** como o tipo chave. Em seguida, **selecione selecione um cofre e uma chave de teclas**.
   
    ![Ecrã de segurança, definições para uma chave gerida pelo cliente](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. Na **tecla Select do ecrã Azure Key Vault,** a subscrição é automaticamente povoada.

    - Para **o cofre key,** pode selecionar um cofre de chaves existente na lista de dropdown.

      ![Selecione a chave do ecrã Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - Também pode selecionar **Criar novo** para criar um novo cofre de chaves. No ecrã do **cofre da chave Create,** insira o grupo de recursos e um nome de cofre chave. Certifique-se de que a proteção **para eliminar** e **purgar** suavemente está ativada. Aceite todos os outros incumprimentos e selecione **Review + Create**.

      ![Criar uma nova definição de Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      Reveja as informações para o seu cofre chave e selecione **Criar**. Espere alguns minutos para a criação do cofre para completar.

      ![Novo ecrã de revisão do Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. Na **tecla Select a partir do ecrã Azure Key Vault,** pode selecionar uma chave existente no cofre de chaves.

    ![Selecione a chave existente a partir do Cofre de Chaves Azure](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    Se pretender criar uma nova chave, **selecione Criar nova**. Tem de usar uma chave RSA. O tamanho pode ser 2048 ou maior. Introduza um nome para a sua nova tecla, aceite as outras predefinições e selecione **Criar**.

      ![Criar uma nova opção chave](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      Será notificado quando a chave for criada no seu cofre.

14. Selecione a **versão** da chave a utilizar e, em seguida, escolha **Selecione**.

      ![Nova chave criada no cofre chave](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    Se pretender criar uma nova versão chave, selecione **Criar novo**.

    ![Abra uma caixa de diálogo para criar uma nova versão chave](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    No novo ecrã **da chave,** escolha as definições para a nova versão chave e selecione **Criar**.

    ![Criar uma nova versão chave](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    As definições **do tipo encriptação** no ecrã **de Segurança** mostram o cofre e a chave da chave.

    ![Chave e cofre chave para uma chave gerida pelo cliente](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. Selecione uma identidade de utilizador que utilizará para gerir o acesso a este recurso. Escolha **Selecionar uma identidade de utilizador.** No painel à direita, selecione a subscrição e a identidade gerida para utilizar. Em seguida, escolha **Selecionar**.

    Uma identidade gerida atribuída pelo utilizador é um recurso autónomo do Azure que pode ser usado para gerir múltiplos recursos. Para obter mais informações, consulte [os tipos de identidade geridos.](/azure/active-directory/managed-identities-azure-resources/overview)  

    Se precisar de criar uma nova identidade gerida, siga as orientações em [Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída pelo utilizador utilizando o portal Azure](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
    
    ![Selecione uma identidade de utilizador](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    A identidade do utilizador é mostrada nas definições **do tipo encriptação.**

    Pode entrar em colapso nas definições do **tipo encriptação** agora.

    ![Uma identidade de utilizador selecionada mostrada nas definições do tipo de encriptação](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. Se pretender ativar a dupla encriptação baseada em software, expanda a **encriptação dupla (para ambientes de alta segurança)** e selecione **Ative a dupla encriptação para a encomenda**. 

    A encriptação baseada em software é realizada para além da encriptação bit AES-256 dos dados na Caixa de Dados.

    > [!NOTE]
    > Permitir esta opção poderia fazer com que o processamento de encomendas e a cópia de dados demorasse mais tempo. Não pode alterar esta opção depois de criar o seu pedido.

    ![Tela de segurança para importação de caixa de dados, encriptação dupla](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    Selecione **Seguinte: Contacte os dados** para continuar.

11. Nos **dados** de Contacto , selecione **+ Adicionar endereço de envio** para introduzir as suas informações de envio.

    ![Adicionar endereço de envio](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. No **endereço Add Shipping,** forneça o seu primeiro e último nome, nome e endereço postal da empresa e um número de telefone válido. Selecione **Validar**. O serviço valida o endereço de envio relativamente à disponibilidade do serviço. Se o serviço estiver disponível para o endereço de envio especificado, receberá uma notificação para o efeito.

    ![Validar endereço de envio](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    Se você está encomendando em uma região onde o envio auto-gerido está disponível, você pode selecionar esta opção. Para obter mais informações sobre o transporte auto-gerido, consulte [use o envio auto-gerido](data-box-portal-customer-managed-shipping.md).

13. **Selecione Adicionar o endereço de envio** assim que os dados de envio tiverem sido validados com sucesso.

14. Nos **dados de Contato,** reveja o seu endereço de envio e endereço de e-mail. O serviço envia notificações por e-mail relativamente a todas as atualizações do estado da encomenda para os endereços de e-mail especificados.

    Recomendamos que utilize um e-mail de grupo para continuar a receber notificações se um administrador sair do grupo.

    ![Detalhes de contacto](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. Selecione **Seguinte: Rever + Encomendar>**. Deve aceitar os termos e condições para proceder à criação da ordem.

16. Selecione **Encomendar**. A encomenda demora alguns minutos a ser criada.

    ![Cometer ordem](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>Encomenda de exportação usando ficheiro XML

Se selecionar **O ficheiro Utilizar o ficheiro XML,** pode especificar recipientes e bolhas específicas (página e bloco) que pretende exportar. Terá de seguir as especificações da [tabela de ficheiros Sample XML](#sample-xml-file) para formatar o seu XML. Os passos abaixo mostram-lhe como utilizar um ficheiro XML para exportar os seus dados:

1. Para **o tipo de exportação**, selecione Use **XML file**. Este é o seu ficheiro XML que especifica blobs específicos e ficheiros Azure que pretende exportar. Para adicionar o ficheiro XML, **selecione Clique aqui para selecionar um ficheiro XML**.

     ![Selecione A opção de exportação, XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. Selecione **+ Recipiente** para criar um recipiente.

    ![Selecione opção de exportação, contentores](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. No **separador New Container** que sai do lado direito do portal Azure, adicione um nome para o recipiente. O nome deve ser em minúscula e pode incluir números e traços '-'. Em seguida, selecione o **nível** de acesso público a partir da caixa de lista de retirada. Recomendamos que escolha **Acesso Privado (não anónimo)** para evitar que outros acedam aos seus dados. Para obter mais informações sobre os níveis de acesso ao contentor, consulte [as permissões de acesso ao contentor.](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)

   ![Selecione a opção de exportação, novas definições de recipiente](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. Selecione **Criar**.

   ![Selecione a opção Exportação, Crie um novo recipiente.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   Se o seu recipiente for criado com sucesso, receberá a seguinte mensagem:

   ![Recipiente criado com sucesso](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. Selecione o recipiente que criou e clique duas vezes nele.

   ![Ver detalhes do recipiente](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. Clicar duas vezes no recipiente trará a vista das propriedades do contentor. Pretende agora anexar (ou navegar) o seu ficheiro XML que contém a sua lista de bolhas e/ou Ficheiros Azure que pretende exportar. Selecione **Carregar**.

   ![Carregar bolha para o recipiente](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. Adicionou com sucesso o ficheiro XML ao recipiente. Apenas blobs e Ficheiros Azure especificados neste XML serão exportados.

   ![Ficheiro XML adicionado ao recipiente](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="track-the-order"></a>Controlar a encomenda

Depois de fazer a encomenda, pode controlar o estado da encomenda a partir do portal do Azure. Vá à sua encomenda de Caixa de Dados e, em seguida, vá ao **Overview** para ver o estado. O portal mostra a encomenda no estado **Encomendado**.

Quando a preparação do dispositivo estiver concluída, a cópia de dados começará a partir das contas de armazenamento selecionadas. O portal mostra a ordem na **cópia de dados em** estado de progresso.

![Ordem de exportação da Caixa de Dados, cópia de dados em curso](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

A Caixa de Dados copia os dados da conta de armazenamento de origem. Uma vez concluída a cópia de dados, a Caixa de Dados fica bloqueada e o portal mostrará a encomenda em Estado **preenchido de Cópia.**

![Ordem de exportação da Caixa de Dados, cópia de dados completa](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

Se o dispositivo não estiver disponível, receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identifica-o para envio e prepara o envio. Durante a preparação do dispositivo, ocorrem as seguintes ações:

* São criadas Partilhas SMB para cada conta de armazenamento associada ao dispositivo.
* Para cada partilha, são geradas credenciais de acesso, como nome de utilizador e palavra-passe.
* O dispositivo está bloqueado e só pode ser acedido utilizando a senha de desbloqueio do dispositivo. Para recuperar a palavra-passe, tem de iniciar sessão na sua conta do portal Azure e selecionar **detalhes do Dispositivo**.

Em seguida, a Microsoft prepara e despacha o seu dispositivo através de uma transportadora regional. Receberá um número de encomenda assim que o dispositivo for enviado. O portal mostra a encomenda no estado **Expedido**.

![Encomenda de exportação da Caixa de Dados expedida](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

Se o envio auto-gerido for selecionado, receberá uma notificação por e-mail com os próximos passos quando o dispositivo estiver pronto para ser recolhido no datacenter. Para obter mais informações sobre o transporte auto-gerido, consulte [o envio auto-gerido.](data-box-portal-customer-managed-shipping.md)

![Envio auto-gerido pronto para recolha](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>Cancelar a encomenda

Para cancelar esta encomenda, no portal Azure, vá ao **Overview** e **selecione Cancelar** a partir da barra de comando.

Depois de escrulhá-la, pode cancelá-la em qualquer ponto antes de a encomenda começar a processar.

Para eliminar uma encomenda cancelada, vá ao **Overview** e selecione **Delete** da barra de comando.

## <a name="sample-xml-file"></a>Arquivo XML da amostra

O xml a seguir mostra um exemplo de nomes blob, prefixos blob e Ficheiros Azure contidos no formato xml que a ordem de exportação utiliza quando seleciona a opção **de ficheiro XML de utilização:**

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Alguns pontos importantes no que diz respeito aos ficheiros XML:

* As etiquetas Xml são sensíveis à caixa e precisam de corresponder exatamente como especificado na amostra acima.
* As etiquetas de abertura e de fecho devem coincidir.
* Etiquetas xml incorretas ou formatação podem levar a falha na exportação de dados.
* Nenhum dado será exportado se o prefixo blob e/ou prefixo do ficheiro for inválido.

### <a name="examples-of-valid-blob-paths"></a>Exemplos de caminhos de bolhas válidos

A tabela a seguir mostra exemplos de caminhos de bolhas válidos:

   | Seletor | Caminho da Bolha | Descrição |
   | --- | --- | --- |
   | Starts with |/ |Exporta todas as bolhas na conta de armazenamento |
   | Starts with |/$root/ |Exporta todas as bolhas no recipiente raiz |
   | Starts with |/contentores |Exporta todas as bolhas em qualquer recipiente que comece com **recipientes** de prefixo |
   | Starts with |/nome do contentor/ |Exporta todas as bolhas em **nome de recipiente** |
   | Starts with |/nome/prefixo do contentor |Exporta todas as bolhas em **nome de recipiente** que começam com **prefixo prefixo** |
   | Igual a |$root/logo.bmp |Exportações blob **logo.bmp** no recipiente raiz |
   | Igual a |8tbpageblob/mydata.txt |Exportações blob **mydata.txt** em contentor **8tbpageblob** |

## <a name="sample-log-files"></a>Ficheiros de registo de amostras

Esta secção fornece ficheiros de registo de amostras que são gerados durante a exportação. Os registos de erro são gerados automaticamente. Para gerar o ficheiro de registo verboso, tem de selecionar **Incluir o registo verboso** no portal Azure ao configurar a ordem de exportação.
Para obter mais informações sobre registos de cópias e verbosos, consulte [os registos de cópias](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
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
``` -->

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
>
> * Pré-requisitos para exportação
> * Encomende uma Caixa de Dados para exportação
> * Acompanhe a ordem de exportação
> * Cancelar a ordem de exportação

Avance para o tutorial seguinte para saber como configurar o Data Box.

> [!div class="nextstepaction"]
> [Configurar o Azure Data Box](./data-box-deploy-set-up.md)
