---
title: Como importar e exportar configurações de configuração AD Connect Azure
description: Este artigo descreve perguntas frequentes para o provisionamento em nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67460c654c854c5a855560dde1d67732fa818c7
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681960"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings"></a>Importar e exportar configurações de configuração AZure AD Connect 

Azure Ative Directory (Azure AD) As implementações de ligação variam de uma instalação de modo express de uma única floresta para implementações complexas que sincronizam em várias florestas utilizando regras de sincronização personalizadas. Devido ao grande número de opções e mecanismos de configuração, é essencial entender quais as definições em vigor e ser capaz de implementar rapidamente um servidor com uma configuração idêntica. Esta funcionalidade introduz a capacidade de catalogar a configuração de um determinado servidor de sincronização e importar as definições para uma nova implementação. Diferentes configurações de sincronização podem ser comparadas para visualizar facilmente as diferenças entre dois servidores ou o mesmo servidor ao longo do tempo.

Cada vez que a configuração é alterada a partir do assistente Azure AD Connect, um novo ficheiro de definições JSON com carimbo temporal é automaticamente exportado para **%ProgramData%\\AADConnect**. O nome do ficheiro de definições é do formulário **Applied-SynchronizationPolicy-*. JSON,** onde a última parte do nome do ficheiro é uma marca de tempo.

> [!IMPORTANT]
> Apenas as alterações efetuadas pelo Azure AD Connect são exportadas automaticamente. Quaisquer alterações efetuadas utilizando o PowerShell, o Gestor de Serviços de Sincronização ou o Editor de Regras de Sincronização devem ser exportadas a pedido, conforme necessário para manter uma cópia atualizada. As exportações a pedido também podem ser utilizadas para colocar uma cópia das definições num local seguro para fins de recuperação de catástrofes.

## <a name="export-azure-ad-connect-settings"></a>Exportar configurações de conexão AD AD de exportação 

Para ver um resumo das definições de configuração, abra a ferramenta Azure AD Connect e selecione a tarefa adicional chamada **Ver ou Exportar Configuração corrente**. Um resumo rápido das suas definições é mostrado juntamente com a capacidade de exportar a configuração completa do seu servidor.

Por predefinição, as definições são exportadas para **%ProgramData%\AADConnect**. Também pode optar por guardar as definições para um local protegido para garantir a disponibilidade em caso de catástrofe. As definições são exportadas utilizando o formato de ficheiro JSON e não devem ser criadas ou editadas à mão para garantir uma consistência lógica. Importar um ficheiro criado à mão ou editado não é suportado e pode levar a resultados inesperados.

## <a name="import-azure-ad-connect-settings"></a>Import Azure AD Connect

Importar configurações previamente exportadas:
 
1. Instale **o Azure AD Connect** num novo servidor.
1. Selecione a opção **Personalizar** após a página **Welcome.**
1. Selecione **Definições de sincronização de importação**. Navegue para o ficheiro de definições JSON previamente exportado.
1. Selecione **Instalar**.

   ![Screenshot que mostra o ecrã de componentes necessários para instalar](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Substitua as definições nesta página como a utilização do SQL Server em vez do LocalDB ou a utilização de uma conta de serviço existente em vez de um VSA predefinido. Estas definições não são importadas a partir do ficheiro de definições de configuração. Estão lá para fins de informação e comparação.

### <a name="import-installation-experience"></a>Experiência de instalação de importação 

A experiência de instalação de importação é mantida intencionalmente simples com entradas mínimas do utilizador para fornecer facilmente a reprodutibilidade de um servidor existente.

Estas são as únicas alterações que podem ser feitas durante a experiência de instalação. Todas as outras alterações podem ser efetuadas após a instalação do assistente Azure AD Connect:
- **Credenciais do Azure Ative Directory**: O nome da conta do Administrador Global Azure utilizado para configurar o servidor original é sugerido por padrão. *Tem de* ser alterado se   quiser sincronizar a informação com um novo diretório.
- **Sposição do utilizador**: As opções de inscrição configuradas para o seu servidor original são selecionadas por predefinição e solicitam automaticamente credenciais ou outras informações necessárias durante a configuração. Em casos raros, pode haver a necessidade de configurar um servidor com diferentes opções para evitar alterar o comportamento do servidor ativo. Caso contrário, selecione **Seguinte** para utilizar as mesmas definições.
- **Credenciais de diretório no local**: Para cada diretório no local incluído nas definições de sincronização, deve fornecer credenciais para criar uma conta de sincronização ou fornecer uma conta de sincronização personalizada pré-criada. Este procedimento é idêntico à experiência de instalação limpa com a exceção de que não pode adicionar ou remover diretórios.
- **Opções de configuração**: Tal como acontece com uma instalação limpa, poderá optar por configurar as definições iniciais para iniciar a sincronização automática ou ativar o modo de realização. A principal diferença é que o modo de encenação é intencionalmente ativado por padrão para permitir a comparação dos resultados de configuração e sincronização antes de exportar ativamente os resultados para Azure.

![Screenshot que mostra o ecrã De Ligar os seus diretórios](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> Apenas um servidor de sincronização pode estar no papel principal e exportar ativamente alterações de configuração para Azure. Todos os outros servidores devem ser colocados no modo de preparação.

## <a name="migrate-settings-from-an-existing-server"></a>Migrar definições a partir de um servidor existente 

Se um servidor existente não suportar a gestão de definições, pode optar por atualizar o servidor no local ou migrar as definições para utilização num novo servidor de paragem.

A migração requer a execução de um script PowerShell que extrai as definições existentes para utilização numa nova instalação.Utilize este método para catalogar as definições do servidor existente e, em seguida, aplique-as num servidor de paragem recentemente instalado.Comparar as definições do servidor original com um servidor recém-criado visualizará rapidamente as alterações entre os servidores.Como sempre, siga o processo de certificação da sua organização para garantir que não é necessária nenhuma configuração adicional.

### <a name="migration-process"></a>Processo de migração 
Para migrar as definições:

1. Inicie **AzureADConnect.msi** no novo servidor de encenação e pare na página de **boas-vindas** do Azure AD Connect.

1. Copie **MigrateSettings.ps1** do diretório microsoft Ad Connect\Tools para uma localização no servidor existente. Um exemplo é C:\configuração, onde a configuração é um diretório que foi criado no servidor existente.

   ![Screenshot que mostra a azure AD Connect diretórios.](media/how-to-connect-import-export-config/migrate1.png)

1. Execute o script como mostrado aqui, e guarde todo o diretório de configuração do servidor de nível inferior. Copie este diretório para o novo servidor de encenação. Tem de copiar toda a pasta **Exported-ServerConfiguration** para o novo servidor.

   ![Screenshot que mostra script no Windows PowerShell. ](media/how-to-connect-import-export-config/migrate2.png)
    ![ Screenshot que mostra a cópia da pasta Exported-ServerConfiguration-_ .](media/how-to-connect-import-export-config/migrate3.png)

1. Inicie **o Azure AD Connect** clicando duas vezes no ícone no ambiente de trabalho. Aceite os Termos de Licença de Software da Microsoft e na página seguinte, **selecione Personalize**.
1. Selecione a caixa **de verificação das definições de sincronização de Importação.** **Selecione Procurar** para navegar na pasta Exported-ServerConfiguration-* copiada. Selecione a MigratedPolicy.jspara importar as definições migradas.

   ![Screenshot que mostra a opção de sincronização de importação.](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>Verificação pós-instalação 

Comparar o ficheiro de definições originalmente importado com o ficheiro de definições exportadas do servidor recém-implantado é um passo essencial para compreender quaisquer diferenças entre o pretendido contra a implantação resultante. A utilização da sua aplicação de comparação de textos lado a lado favorita produz uma visualização instantânea que realça rapidamente quaisquer alterações desejadas ou acidentais.

Embora muitos passos de configuração manual anteriormente sejam eliminados, você ainda deve seguir o processo de certificação da sua organização para garantir que não é necessária configuração adicional. Esta configuração pode ocorrer se utilizar definições avançadas, que não são atualmente capturadas nesta versão da gestão de definições.

Aqui estão as limitações conhecidas:
- **Regras de sincronização**: A precedência de uma regra personalizada deve estar na gama reservada de 0 a 99 para evitar conflitos com as regras padrão da Microsoft. A colocação de uma regra personalizada fora do alcance reservado pode resultar na mudança da sua regra personalizada à medida que as regras padrão são adicionadas à configuração. Um problema semelhante ocorrerá se a sua configuração contiver regras padrão modificadas. Modificar uma regra padrão é desencorajado, e a colocação de regras é suscetível de ser incorreta.
- **Reprodução do dispositivo**: Estas definições estão catalogadas. Não são atualmente aplicados durante a configuração. Se o registo do dispositivo tiver sido ativado para o seu servidor original, deve configurar manualmente a funcionalidade no servidor recém-implantado.
- **Tipos de objetos sincronizados**: Embora seja possível limitar a lista de tipos de objetos sincronizados (tais como utilizadores, contactos e grupos) utilizando o Gestor de Serviço de Sincronização, esta funcionalidade não é suportada atualmente através de definições de sincronização. Depois de terminar a instalação, deve reaplicar manualmente a configuração avançada.
- **Perfis de execução personalizados**: Embora seja possível modificar o conjunto predefinido de perfis de execução utilizando o Gestor de Serviço de Sincronização, esta funcionalidade não é suportada atualmente através de definições de sincronização. Depois de terminar a instalação, deve reaplicar manualmente a configuração avançada.
- **Configurar a hierarquia de provisionamento**: Esta característica avançada do Gestor de Serviços de Sincronização não é suportada através de definições de sincronização. Deve ser reconfigurado manualmente depois de terminar a colocação inicial.
- **Serviços da Federação de Diretório Ativo (AD FS) e autenticação PingFederate**: Os métodos de inscrição associados a estas características de autenticação são automaticamente pré-selecionados. Deve fornecer interativamente todos os outros parâmetros de configuração necessários.
- **Uma regra de sincronização personalizada desativada será importada conforme ativado:** Uma regra de sincronização personalizada desativada é importada conforme ativado. Certifique-se de desativá-lo também no novo servidor.

 ## <a name="next-steps"></a>Passos seguintes

- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições expressas](how-to-connect-install-express.md)
- [Definições personalizadas](how-to-connect-install-custom.md)
- [Instalar os agentes do Azure AD Connect Health](how-to-connect-health-agent-install.md) 
