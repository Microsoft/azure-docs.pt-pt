---
title: Importar palavras-passe na aplicação Microsoft Authenticator - Azure AD
description: Como importar palavras-passe na aplicação Microsoft Authentication de gestores de senhas populares.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: b0d7eeeb840a3efc560c20310b38bee93a038795
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056268"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Importar palavras-passe para a aplicação Microsoft Authenticator

O Microsoft Authenticator suporta a importação de senhas do Google Chrome, Firefox, LastPass, Bitwarden e Roboform. Se a Microsoft não suportar atualmente o seu gestor de passwords existente, pode [introduzir manualmente credenciais de entrada no nosso modelo CSV](https://go.microsoft.com/fwlink/?linkid=2134938). Para importar as suas palavras-passe existentes e geri-las na aplicação Authenticator, basta exportar as suas palavras-passe do seu gestor de passwords existentes para o nosso formato de valores separados por vírgula (CSV). Em seguida, importe o CSV exportado para Autenticador na nossa extensão do navegador Chrome ou diretamente para a aplicação Authenticator (Android e iOS).

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Importação do Google Chrome ou Android Smart Lock

Pode importar as suas palavras-passe do Google Chrome ou do Android Smart Lock para Autenticador no seu smartphone ou no seu computador de secretária. Pode:

- [Importação do Chrome para Android e iOS](#import-from-chrome-on-android-and-ios)
- [Importação do navegador de ambiente de trabalho Chrome](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Importação do Chrome para Android e iOS

Os utilizadores do Google Chrome em telemóveis Android e Apple podem importar as suas palavras-passe diretamente do seu telemóvel com poucos passos simples.

1. Instale a aplicação Authenticator no seu telemóvel e abra o **separador Palavras-passe.**

1. Inscreva-se no Google Chrome no seu telefone.

1. Toque ![ no menu de elipses do Google Chrome ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) no canto superior direito para telefones Android ou em inferior direita para dispositivos iOS e, em seguida, toque em **Definições.**

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Localização do menu do Google Chrome Settings](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Ícone do menu de configurações do Google Chrome](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. Em **Definições,** abra **palavras-passe**.

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Localização do comando de Passwords Andoid Chrome](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Localização do comando de passwords do Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. Nos dispositivos Android, toque ![ no menu de elipses do Google Chrome ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) no canto superior direito para telefones Android, ou na parte inferior direita para dispositivos iOS, e depois toque em **senhas de exportação**.

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Localização de senhas de exportação do Android Chrome Export](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Localização de senhas de exportação do Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   Deve fornecer um PIN, impressão digital ou reconhecimento facial. Confirme a sua identidade e toque **novamente nas palavras-passe de exportação** para começar a exportar.

1. Depois de exportadas as palavras-passe, o Chrome pede-lhe que escolha qual a aplicação para a qual está a importar. Selecione **Autenticador** para começar a importar senhas. Será informado sobre o estado de importação quando estiver completo.

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Localização de senhas de importação do Android Chrome](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Localização de senhas de importação do Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Importação do navegador de ambiente de trabalho Chrome

Antes de começar, tem de instalar e iniciar súm na extensão do [Microsoft Autofill](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) no seu navegador Chrome.

1. Abra [o Gestor de Passwords do Google](https://passwords.google.com) em qualquer navegador. Se ainda não o fez, inscreva-se na sua conta do Google.

1. Selecione o ícone de engrenagem ![Ícone de engrenagem de gestor de palavra-passe de desktop](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) para abrir para a página de definições de palavra-passe.

1. Selecione **Export**, em seguida, na página seguinte selecione **Export** again para começar a exportar suas palavras-passe. Forneça a sua palavra-passe do Google quando solicitado para confirmar a sua identidade. Será informado sobre o estado de importação quando estiver completo.

   ![Local de comando de senhas de exportação de navegador do Chrome do Desktop Chrome](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. Abra a extensão do Chrome de enchimento automático e **selecione Definições**.

   ![Localização das definições de extensão de preenchimento automático do navegador Desktop Chrome](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. Selecione **Dados de Importação** para abrir um diálogo. Em seguida, **selecione Choose File** para localizar e importar o ficheiro CSV.

   ![Desktop Chrome browser Importar localização de dados CSV](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Importação do Firefox

O Firefox permite exportar apenas palavras-passe do navegador desktop, por isso certifique-se de que tem acesso ao navegador de ambiente de trabalho do Firefox antes de importar senhas do Firefox.

1. Inscreva-se na versão mais recente do Firefox no seu ambiente de trabalho e selecione o ![Menu "hambúrguer" do Firefox](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) menu a partir da direita superior do ecrã.

1. Selecione **Logins e Passwords**.

   ![Início de sessão de login e palavras-passe do navegador Desktop Firefox](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. Na página Firefox Lockwise, selecione o menu de ![ elipses ](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) firefox, selecione **'Logins'** de exportação e, em seguida, confirme a sua intenção selecionando **Export**. É-lhe pedido que se identifique introduzindo o seu PIN, palavra-passe do dispositivo ou digitalizando as suas impressões digitais. Uma vez identificado com sucesso, o Firefox exporta as suas palavras-passe em formato CSV para o local selecionado.

   ![Localização de senhas de exportação do navegador Desktop Firefox](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. Pode importar as suas palavras-passe para autenticador a partir de um browser de ambiente de trabalho ou em telefones iOS ou Android. Para importar para a aplicação Authenticator no seu telefone:

      1. Transfira o ficheiro CSV exportado para o seu telefone Android ou iOS utilizando uma forma preferida e segura e, em seguida, descarregue-o. Em seguida, partilhe o ficheiro CSV com a aplicação Authenticator para iniciar a importação.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Localização de senhas de importação do Android Chrome](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Localização de senhas de importação do Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Depois de importar com sucesso a sua palavra-passe para Autenticador, elimine o ficheiro CSV do seu ambiente de trabalho ou telemóvel.

## <a name="import-from-lastpass"></a>Importação do LastPass

O LastPass suporta senhas de exportação apenas a partir de um navegador de ambiente de trabalho, por isso certifique-se de que tem acesso a um browser de ambiente de trabalho antes de começar a importar senhas.

1. Inscreva-se [no site do LastPass](https://lastpass.com) e selecione **Opções Avançadas** e, em seguida, selecione **Export**.

   ![Localização de senhas de exportação do LastPass do Desktop LastPass](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. Identifique-se quando solicitado, fornecendo a sua senha principal. Depois disso, verá as senhas exportadas na página web.

1. Copie o conteúdo da página web.

1. Abra o Bloco de Notas (ou o seu editor de texto favorito) e cole o conteúdo copiado.

1. Guarde este ficheiro de  bloco de notas selecionando &gt; **Ficheiros Guarde como**. Forneça um nome que termine com ".csv" (como LastPass.csv) num local seguro no seu ambiente de trabalho.

   ![Desktop LastPass salvar ficheiro CSV](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. Pode importar as suas palavras-passe para Autenticador num browser de ambiente de trabalho ou em iOS ou telefones Android. Para importar para a aplicação Authenticator no seu telefone:

      1. Transfira o ficheiro CSV exportado no seu smartphone utilizando uma forma preferencial e segura e, em seguida, descarregue-o. Em seguida, partilhe o ficheiro CSV com a aplicação Authenticator para iniciar a importação.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Localização de senhas de importação do Android LastPass](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Localização de senhas de importação do Apple LastPass](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Depois de importar com sucesso a sua palavra-passe para Autenticador, elimine o ficheiro CSV do seu ambiente de trabalho ou telemóvel.

## <a name="import-from-bitwarden"></a>Importação de Bitwarden

A Bitwarden suporta senhas de exportação apenas a partir de um navegador de ambiente de trabalho, por isso certifique-se de ter acesso a um navegador de ambiente de trabalho antes de começar a importar senhas.

1. Inscreva-se https://vault.bitwarden.com/ e selecione **Tools** &gt; **Export vault**. Escolha o formato de ficheiro como CSV, forneça a sua senha principal e, em seguida, selecione **o cofre de exportação** para começar a exportar.

   ![Localização do cofre de exportação bitwarden](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. Pode importar as suas palavras-passe para Autenticador num browser de ambiente de trabalho ou em iOS ou telefones Android. Para importar para a aplicação Authenticator no seu telefone:

      1. Transfira o ficheiro CSV exportado no seu smartphone utilizando uma forma preferencial e segura e, em seguida, descarregue-o. Em seguida, partilhe o ficheiro CSV com a aplicação Authenticator para iniciar a importação.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Localização de senhas de importação de Android Bitwarden](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Localização de senhas de importação apple Bitwarden](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Depois de importar com sucesso a sua palavra-passe para Autenticador, elimine o ficheiro CSV do seu ambiente de trabalho ou telemóvel.

## <a name="import-from-roboform"></a>Importação de Roboform

A Roboform permite exportar apenas palavras-passe a partir da sua aplicação para desktop, por isso, certifique-se de que tem acesso à aplicação Roboform num ambiente de trabalho antes de iniciar a importação.

1. Inicie o RoboForm a partir do seu cliente de ambiente de trabalho e faça login na sua conta.

1. Selecione **Opções** no menu **Roboform.**

   ![Menu de opções roboform de desktop](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. Selecione Conta & Exportação **de Dados.** &gt; 

   ![Local de comando de exportação roboform de desktop Roboform](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. Escolha um local seguro para guardar o seu ficheiro exportado. Selecione **Logins** como o tipo **de Dados** e selecione o ficheiro CSV como formato e, em seguida, selecione **Export**.

   ![Caixa de diálogo de exportação roboform de ambiente de trabalho](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. Confirme a sua intenção e o ficheiro CSV é depois exportado para o local selecionado.

   ![Caixa de diálogo de confirmação de exportação roboform de ambiente de trabalho Roboform](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. Pode importar as suas palavras-passe para Autenticador num browser de ambiente de trabalho ou em iOS ou telefones Android. Para importar para a aplicação Authenticator no seu telefone:

      1. Transfira o ficheiro CSV exportado no seu smartphone utilizando uma forma preferencial e segura e, em seguida, descarregue-o. Em seguida, partilhe o ficheiro CSV com a aplicação Authenticator para iniciar a importação.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Localização de senhas de importação de Roboform Android Roboform](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Localização de senhas de importação de Apple Roboform](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Depois de importar com sucesso a sua palavra-passe para Autenticador, elimine o ficheiro CSV do seu ambiente de trabalho ou telemóvel.

## <a name="import-by-creating-a-csv"></a>Importar através da criação de um CSV

Se as etapas para importar palavras-passe do seu gestor de passwords não estiverem listadas neste artigo, pode criar um CSV que pode utilizar para importar as suas palavras-passe para autenticador. A Microsoft recomenda que siga estes passos num ambiente de trabalho para facilitar a formatação.

1. No seu ambiente de trabalho, [descarregue e abra o nosso modelo de importação.](https://go.microsoft.com/fwlink/?linkid=2134938) Se é um utilizador apple iPhone, Safari e Keychain, pode agora saltar para o passo 4.

1. Exporte as suas palavras-passe do seu gestor de passwords existente num ficheiro CSV não encriptado.

1. Copie as colunas relevantes do seu CSV exportado para o CSV do modelo e, em seguida, guarde.

1. Se não tiver um CSV exportado, pode copiar cada login do seu gestor de passwords existente para o CSV do modelo. Não remova nem altere a linha do cabeçalho. Quando terminar, verifique a integridade dos seus dados antes de iniciar o próximo passo.

1. Pode importar as suas palavras-passe para Autenticador num browser de ambiente de trabalho ou em iOS ou telefones Android. Para importar para a aplicação Authenticator no seu telefone:

      1. Transfira o ficheiro CSV exportado no seu smartphone utilizando uma forma preferencial e segura e, em seguida, descarregue-o. Em seguida, partilhe o ficheiro CSV com a aplicação Authenticator para iniciar a importação.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Localização de senhas de importação de CSV Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Localização de senhas de importação de CSV da Apple](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Depois de importar com sucesso a sua palavra-passe para Autenticador, elimine o ficheiro CSV do seu ambiente de trabalho ou telemóvel.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

A causa mais comum das importações falhadas é a formatação incorreta no ficheiro CSV. Pode tentar os seguintes passos para resolver o problema.

- Consulte este artigo para ver se já apoiamos a importação de senhas do seu gestor de passwords atuais. Se o fizermos, poderá querer voltar a tentar a importação seguindo os passos mencionados para o seu respetivo fornecedor.

- Se não apoiarmos atualmente a importação do formato do seu gestor de passwords, poderá voltar a tentar [criando manualmente o seu ficheiro CSV](#import-by-creating-a-csv).

- Pode verificar a integridade dos dados do CSV com as seguintes sugestões:

  - A primeira fila deve conter um cabeçalho com três colunas: **url,** **username** e **password**.

  - Cada linha deve conter um valor nas colunas **url** e **passwords.**

- Pode recriar o CSV colando o seu conteúdo no [ficheiro de modelo CSV](https://go.microsoft.com/fwlink/?linkid=2134938).

- Se nada mais funcionar, por favor reporte o seu problema usando o link **Enviar feedback** a partir das definições da aplicação Authenticator.
