# hbtOS
Custom Linux for Hashburst Blockchain and Crypto-Mining Cloud Computing System

# Hashburst Cloud OS

## 1. Preparazione dell'Ambiente
Scarica e Installa Linux Lite 7.0:
Scarica l'immagine ISO da https://mirror.koddos.net/linuxlite/isos/7.0/
Usa un tool come Rufus o Etcher per creare l'unità USB avviabile.
Installa Linux Lite 7.0 sulla tua macchina. Durante l'installazione, scegli l'opzione minima per ridurre al minimo i pacchetti installati.

  Rimozione di X11:
  Rimuovi l'interfaccia grafica X11 se installata:
  
  sh

                          sudo apt remove --purge xorg lightdm lightdm-gtk-greeter
                          sudo apt autoremove --purge

## 2. Installazione degli Strumenti Necessari

Aggiornamento del Sistema:

sh

                          sudo apt update
                          sudo apt upgrade -y

Installazione di Git, Curl, Wget, Node.js e npm:

                          sudo apt install git curl wget nodejs npm -y

Verifica dell’installazione di node e npm:

                          node -v 
                          
                          npm -v

## 3. Installazione dei Driver Nvidia

Aggiunta del Repository dei Driver Nvidia:

sh

                          sudo add-apt-repository ppa:graphics-drivers/ppa
                          sudo apt update

Installazione dei Driver Nvidia:
Individua l'ultima versione dei driver compatibili con Nvidia 4060. Di solito, i driver più recenti supportano le schede più recenti.

sh

                          sudo apt install nvidia-driver-515 -y
                          sudo reboot

## 4. Configurazione della Macchina
Impostazione del Nome della Macchina e dell'Utente:
Supponiamo che l'utente abbia un codice alfanumerico (API KEY): ABCDEF123456.
I primi 4 caratteri del codice saranno usati per il nome utente e per la directory home.

sh

                          USER_CODE="ABCDEF123456"
                          USER_NAME="tow${USER_CODE:0:4}"
                          
                          sudo useradd -m -d /home/$USER_NAME $USER_NAME
                          echo "$USER_NAME:password" | sudo chpasswd

Configurazione del Nome del Sistema:

sh

                          sudo hostnamectl set-hostname "$USER_CODE"
                          echo "127.0.0.1 $USER_CODE" | sudo tee -a /etc/hosts

## 5. Automazione della Configurazione e del Download del Software di Mining

Creazione dello Script starter.sh:

Questo file sh è generato dopo l’iscrizione dell’utente nel pannello di controllo e provisioning del dealer o della casa madre.

Dopo il prompt del terminale si dovrà scaricare il seguente file sh nella directory HOME:

                          sudo wget -O <API KEY> https://hashburst.io/nodes/<DEALER>/<API KEY>

poi eseguire i seguenti comandi:

sh

                          sudo chmod +x <API KEY> 

con cui si attribuisce il permesso di esecuzione e poi:

                          sudo bash <API KEY> 

con il quale si genera il file “starter.sh” contenente il seguente script:

sh

                          #!/bin/bash
                          
                          USER_CODE="ABCDEF123456"
                          USER_NAME="tow${USER_CODE:0:4}"
                          CLUSTER_CODE="cluster_code_here"
                          
                          HOME_DIR="/home/$USER_NAME"
                          CONFIG_DIR="$HOME_DIR/configs"
                          BIN_DIR="$HOME_DIR/BIN"
                          
                          # Crea le directory necessarie
                          mkdir -p $CONFIG_DIR $BIN_DIR
                          
                          # Scarica le configurazioni dal sito hashburst.io
                          wget -O "$CONFIG_DIR/$CLUSTER_CODE+${USER_CODE:0:4}" "https://hashburst.io/nodes/$CLUSTER_CODE+${USER_CODE:0:4}"
                          wget -O "$CONFIG_DIR/$USER_CODE" "https://hashburst.io/nodes/$USER_CODE"
                          
                          # Scarica e installa RainbowMiner
                          git clone https://github.com/RainbowMiner/RainbowMiner.git $BIN_DIR/RainbowMiner
                          cd $BIN_DIR/RainbowMiner
                          ./install.sh
                          
                          # Scarica e installa il miner JS per Dogecoin
                          wget -O "$BIN_DIR/DogeconBrowserMiner.js" "https://github.com/hashburst/cryptominers/blob/main/DogeconBrowserMiner.js"
                          cd $BIN_DIR
                          npm install puppeteer
                          
                          # Avvia RainbowMiner con le configurazioni scaricate
                          bash "$CONFIG_DIR/$CLUSTER_CODE+${USER_CODE:0:4}"
                          bash "$CONFIG_DIR/$USER_CODE"
                          
                          # Avvia RainbowMiner
                          cd $BIN_DIR/RainbowMiner
                          ./start.sh
                          
                          # Avvia il miner JS per Dogecoin
                          node $BIN_DIR/DogeconBrowserMiner.js


Rendere starter.sh Eseguibile:

sh

                          chmod +x starter.sh

Configurazione dell'Avvio Automatico:

Aggiungere starter.sh all'inizio del processo di avvio di Linux.

sh

                          sudo cp starter.sh /usr/local/bin/starter.sh
                          sudo chmod +x /usr/local/bin/starter.sh
                          
                          sudo nano /etc/systemd/system/starter.service

Incolla il seguente contenuto nel file starter.service:

ini

                          [Unit]
                          Description=Starter Service
                          After=network.target
                          
                          [Service]
                          Type=simple
                          ExecStart=/usr/local/bin/starter.sh
                          
                          [Install]
                          WantedBy=multi-user.target

Abilita il servizio:

sh

                          sudo systemctl enable starter.service
                          sudo systemctl start starter.service

Riavvio e spegnimento:

sh

per il riavvio:

                          reboot

per lo spegnimento, digitare:

                          init 0
