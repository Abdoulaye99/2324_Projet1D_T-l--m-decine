# 2324_Projet1D_Telemedecine 

## Introduction
Ce tutoriel vous guidera à travers les étapes nécessaires pour utiliser un capteur de pulsation avec un microcontrôleur STM32 et afficher les résultats sur une application mobile. Nous couvrirons la configuration matérielle, le code du microcontrôleur, et la création de l'application mobile.

## Tableau de Bord


#### Semaine 1 : Planification et Configuration Initiale
- **Objectifs :**
  - Définir les fonctionnalités essentielles.
  - Configurer l'environnement de développement.
  - Mettre en place la communication entre le STM32 et l'application mobile.
- **Tâches :**
  - Définir les fonctionnalités principales (affichage en temps réel).
  - Choisir les outils et technologies.
  - Configurer le microcontrôleur STM32 et les connexions matérielles.

#### Semaine 2 : Développement de l'Interface Mobile
- **Objectifs :**
  - Créer une application mobile pour recevoir et afficher les données.
  - Assurer la communication Bluetooth entre le STM32 et l'application mobile.
- **Tâches :**
  - Créer l'interface utilisateur de l'application mobile.
  - Implémenter la réception des données via Bluetooth.
  - Tester la réception et l'affichage des données sur l'application mobile.

#### Semaine 3 : Développement du Backend Minimaliste
- **Objectifs :**
  - Mettre en place un backend simple pour gérer les données.
- **Tâches :**
  - Transmettre les données en temps réel au tableau de bord.
  - Tester les endpoints.

#### Semaine 4 : Développement du Frontend Simple
- **Objectifs :**
  - Développer un frontend pour afficher les données en temps réel.
- **Tâches :**
  - Développer l'interface utilisateur du tableau de bord.
  - Implémenter la réception des données en temps réel.
  - Afficher les valeurs de fréquence cardiaque.
  - Tester l'affichage des données.

#### Semaine 5 : Tests, Déploiement et Documentation
- **Objectifs :**
  - Tester le système complet.
  - Déployer le tableau de bord.
  - Documenter le projet.
- **Tâches :**
  - Tester le système complet (application mobile, backend, frontend).
  - Corriger les bugs et améliorer la stabilité.



## Tutoriel

### Matériel nécessaire
- Microcontrôleur STM32 
- Capteur de pulsation ( un capteur de fréquence cardiaque)
- Module Bluetooth (HC-05)
- Câbles de connexion
  
### Logiciels nécessaires
- STM32CubeIDE
- Application mobile pour Android (MIT App Inventor)

### Étape 1 : Configuration matérielle
1. **Connecter le capteur de pulsation au STM32 :**
   - Connectez le VCC du capteur de pulsation au 3.3V du STM32.
   - Connectez le GND du capteur au GND du STM32.
   - Connectez la sortie signal du capteur à un port analogique du STM32 (par exemple, PA0).

2. **Connecter le module Bluetooth au STM32 :**
   - Connectez le VCC du module Bluetooth au 3.3V du STM32.
   - Connectez le GND du module Bluetooth au GND du STM32.
   - Connectez le TX du module Bluetooth au RX du STM32 (par exemple, PA10).
   - Connectez le RX du module Bluetooth au TX du STM32 (par exemple, PA9).

### Étape 2 : Code du Microcontrôleur

#### Configuration de l'environnement STM32CubeIDE
1. Créez un nouveau projet STM32 dans STM32CubeIDE.
2. Configurez les broches correspondantes pour le capteur de pulsation et le module Bluetooth dans le fichier de configuration .ioc.
3. Générer le code de base.

#### Code principal (main.c)
Ajoutez le code suivant pour lire les données du capteur de pulsation et envoyer les résultats via Bluetooth :

```c
#include "main.h"
#include "stm32f1xx_hal.h"
#include "usart.h"
#include "adc.h"

void SystemClock_Config(void);
static void MX_GPIO_Init(void);
static void MX_ADC1_Init(void);
static void MX_USART1_UART_Init(void);

ADC_HandleTypeDef hadc1;
UART_HandleTypeDef huart1;

int main(void) {
    HAL_Init();
    SystemClock_Config();
    MX_GPIO_Init();
    MX_ADC1_Init();
    MX_USART1_UART_Init();

    while (1) {
        HAL_ADC_Start(&hadc1);
        if (HAL_ADC_PollForConversion(&hadc1, HAL_MAX_DELAY) == HAL_OK) {
            uint32_t adcValue = HAL_ADC_GetValue(&hadc1);
            char buffer[10];
            sprintf(buffer, "%lu\n", adcValue);
            HAL_UART_Transmit(&huart1, (uint8_t*)buffer, strlen(buffer), HAL_MAX_DELAY);
        }
        HAL_ADC_Stop(&hadc1);
        HAL_Delay(1000); // Lire les données toutes les secondes
    }
}

// Configuration de l'horloge système, GPIO, ADC et UART
void SystemClock_Config(void) {
    // Configuration de l'horloge
}

static void MX_ADC1_Init(void) {
    // Initialisation de l'ADC
}

static void MX_USART1_UART_Init(void) {
    // Initialisation de l'UART
}

static void MX_GPIO_Init(void) {
    // Initialisation des GPIO
}
```

### Étape 3 : Création de l'application mobile

#### Utilisation de MIT App Inventor
1. **Créez un nouveau projet :**
   - Allez sur [MIT App Inventor](http://appinventor.mit.edu).
   - Créez un nouveau projet.

2. **Ajoutez des composants à l'interface utilisateur :**
   - Ajoutez un label pour afficher la fréquence cardiaque.
   - Ajoutez un bouton pour se connecter au module Bluetooth.
   - Ajoutez un composant Bluetooth Client.

3. **Configurez les blocs de code :**
   - Configurez le bouton pour se connecter au module Bluetooth.
   - Configurez un timer pour lire les données reçues du module Bluetooth et les afficher dans le label.

```blocks
// Lorsque le bouton est cliqué, connecter au module Bluetooth
when BoutonConnecter.Click do
    call BluetoothClient1.Connect(address_of_your_HC_05)

// Lire les données du Bluetooth et les afficher
when BluetoothClient1.AfterReceiving do
    set LabelFrequenceCardiaque.Text to BluetoothClient1.Read()
```

### Conclusion
Vous avez maintenant un système fonctionnel qui lit les données de pulsation à partir d'un capteur connecté à un microcontrôleur STM32 et envoie ces données à une application mobile via Bluetooth. Vous pouvez développer davantage ce projet en ajoutant des fonctionnalités telles que l'enregistrement des données ou des alertes en cas de fréquence cardiaque anormale.
