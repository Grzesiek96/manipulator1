#include <Servo.h> // dołaczenie biblioteki sterującej serwami
//deklaracja poszczegolnych serw(kazde serwo odpowiada za oddzielne ramie)
Servo serwo1;
Servo serwo2;
Servo serwo3;
Servo serwo4;
Servo serwo5;//odpowiada za chwytak
//tablica przechowujaca poszczegolne serwa
Servo tablica_serw[5] = {serwo1, serwo2, serwo3, serwo4, serwo5};

//===================deklaracjacja pinow arduino do poszczegolnyh zmiennych=============
//przyciski sterujace wyborem ramienia
#define przycisk_1 13
#define przycisk_2 12
#define przycisk_3 8
#define przycisk_4 0
#define przycisk_5 7 // chwytak
#define przycisk_6 4 // pozwalajacy na zmiane fazy pracy (wyrozniamy 3 fazy "ruchy ustawcze", "nauczenie", "odwzorowanie ruchu"
#define potencjometr A0 //odczyt analogowy z potencjometru do sterowania
//deklaracja pinow sterujacych serw
#define serwo_1 11
#define serwo_2 10
#define serwo_3 9
#define serwo_4 5
#define serwo_5 6
//deklaracja diód sygnalizujacych obecną faze pracy
#define dioda_ustawczy 1
#define dioda_zapis 2
#define dioda_odczyt 3
//======================== struktury przechowujace polozenia oraz kolejnosc wyboru ramion ===========
typedef struct pamiec_serwa{//struktura przechowujaca nauczone polozenia serw
  int rozmiar = 100;//rozmiar tabeli
  int indeks = 0;//obecny indeks tabeli
  int *wskaznik;//wskaźnik do komórki tabeli
};
struct tablica_pamieci{//struktura przechowujaca tabele położeń
  int rozmiar;//rozmiar tabeli
  struct wskaznik *wskaznik;//odwolanie do wskaźnika pamieci_serw
};
//=================== zmienne globalne====================
int ilosc_wcisniec = 0;//do zliczania klikniec serw do pamieci (oprocz serwa)
int ilosc_wcisniec2 = 0;// zlicza wszystkie klikniecia
int numer_serwa = 0;//przechowuje numer aktualnie pracującego serwa
int faza_pracy = 0;//przechowuje numer aktualnej fazy pracy
int tablica_wcisniec[10];//tablica przechowujaca kolejnosc wciśnięć s
int *wsk_kolejnosc = tablica_wcisniec;
int otwarcie_chwytaka = 1;//chwytak otwarty
int ostatnia_pozycja[5]; //tablica do zapamiętywania ostatnich pozycji ruchów ustawczych
int *ostatnia = ostatnia_pozycja;

//tablice do przechowywania odczytow z potencjometru
int tablica1[100];
int tablica2[100];
int tablica3[100];
int tablica4[100];
int tablica5[100];

pamiec_serwa tablica_pam[5];//globalna deklaracja tablicy struktur
//================= funkcje przyciskow (zliczajace klikniecia) - do wyboru ktorym serwem chcemy sterowac ========= 
void przycisk1(){
  if(digitalRead(przycisk_1) == LOW){
    tablica_wcisniec[ilosc_wcisniec2] = 1;
    ilosc_wcisniec++;//jest to ilosc wcisniec dla serw ktorych polozenie jest zapisywane
    ilosc_wcisniec2++;//ilosc wcisniec wszystkich serw
    numer_serwa = 1;    
  }  
}
void przycisk2(){
  if(digitalRead(przycisk_2) == LOW){
    tablica_wcisniec[ilosc_wcisniec2] = 2;
    ilosc_wcisniec++;
    ilosc_wcisniec2++;
    numer_serwa = 2;   
  }  
}
void przycisk3(){
  if(digitalRead(przycisk_3) == LOW){
    tablica_wcisniec[ilosc_wcisniec2] = 3;
    ilosc_wcisniec++;
    ilosc_wcisniec2++;
    numer_serwa = 3;      
  }
}  

void przycisk4(){
  if(digitalRead(przycisk_4) == LOW){
    tablica_wcisniec[ilosc_wcisniec2] = 4;
    ilosc_wcisniec++;
    ilosc_wcisniec2++;
    numer_serwa = 4;     
  }  
}
void przycisk5(){//przycisk odpowiedzialny za chwytanie
  if(digitalRead(przycisk_5) == LOW){
    tablica_wcisniec[ilosc_wcisniec2] = 5;
    ilosc_wcisniec2++;
    numer_serwa = 5;      
  }  
}
//=========== funkcja zmieniajaca faze pracy ======
int przycisk6(){
  if(digitalRead(przycisk_6) == LOW){
    faza_pracy++;  
    delay(150);
    return 6;    
  }  
}
//wyswietlanie
void wyswietlanie(int ilosc_wcisniec){
  if(ilosc_wcisniec == 5){
    for(int i = 0; i <5; i++){
      Serial.println(tablica_wcisniec[i]);
      delay(1000); 
    }
  }
}
//===================funkcje obslugujace sterowanie robota=============
//funkcja zapisująca poszczególne położenia robota "nauczanie"
void zapis_polozen(int numer_serwa, int ilosc_wcisniec){
  while(ilosc_wcisniec>=1 and ilosc_wcisniec <= 5){ 
    int a = numer_serwa -1;//zmienna sluzaca do odwolywania sie do tablicy serw
    int numer = ilosc_wcisniec- 1; // zmienna sluzaca do odwolywania sie do tablicy pamieci serw   
    if(tablica_pam[numer].indeks >= tablica_pam[numer].rozmiar){
      //digitalWrite(dioda_zapis,LOW);
      Serial.println("zapis skonczony");
      break;
    }
    Serial.println(a);    
    int wartosc = 0;
    int NewAnalogOut = 0;
    wartosc = analogRead(potencjometr);
    NewAnalogOut = map(wartosc,0,1023,0,180);
    *tablica_pam[numer].wskaznik = NewAnalogOut;//zapis do pamieci
    tablica_serw[a].write(NewAnalogOut);
    Serial.println(NewAnalogOut);
    int b = *tablica_pam[numer].wskaznik;
    Serial.println(b);
    *tablica_pam[numer].wskaznik++;  //zapisuje numer do 
    tablica_pam[numer].indeks++;  
    delay(100);//opoznienie, ktore powoduje zmniejszenie szybkosci zapisu położeń do tabeli    
  }
}
//funkcja sterujaca owieraniem i zamykaniem chwytaka
void ruch_chwytaka(int otwarcie_chwytaka){
    if(otwarcie_chwytaka == 1){
      serwo5.write(160);//pozycja odpowiadajaca otwarciu
      otwarcie_chwytaka = 0;
      numer_serwa = 0;
    }
      else{
      serwo5.write(20);//pozycja odpowiadajaca zamknieciu
      otwarcie_chwytaka = 1;   
      numer_serwa = 0;
    } 
}
//funkcja odczytu 
void odczyt_polozen(int a, int tablica_wcisniec[]){//jest to wskaznik do numeru serwa
  int c = tablica_wcisniec[a];
  delay(100);
     while(1){
      if(tablica_pam[a].indeks > tablica_pam[a].rozmiar){
        *wsk_kolejnosc++;//wskaznik do tablicy wscisniec (gdy zostanie zczytana pierwsza komorka tabeli przechowujacej tabele wskaznik zostanie przesuniety na następną komórkę
        break;
      }        
        Serial.println(*wsk_kolejnosc);//jest to wskaznik do tablicy ktora przechowuje kolejnosc zapisanych serw
        tablica_serw[*wsk_kolejnosc - 1].write(*tablica_pam[a].wskaznik);
        int b =*tablica_pam[a].wskaznik;
        Serial.println(b);
        delay(100);
        *tablica_pam[a].wskaznik++;
        tablica_pam[a].indeks++;
      }
}
//funkcja sluzaca do ustawiania ruchów ustawczych robota
void ruchy_ustawcze(int numer_serwa){//funkcja do ustawienia ramion robota we właściwych pozycjach oraz zapisujaca pozycje koncowe
      int a = numer_serwa-1;
      int wartosc = 0;
      int NewAnalogOut = 0;
      wartosc = analogRead(potencjometr);
      NewAnalogOut = map(wartosc,0,1023,0,180);
      Serial.println(NewAnalogOut);
      tablica_serw[a].write(NewAnalogOut);
      ostatnia_pozycja[a] = NewAnalogOut ;//ostatnia pozycja przed wybraniem kolejnego ramienia (robot będzie musiał do niej wrócić, tak aby mógł wykonać nauczone ruchy z pozycji początkowych)
      delay(100);
}
//funkcja wykonujaca powrot ramion do "pozycji poczatkowych"
void powrot_do_poczatkowych(){
  for(int i = 0; i<5; i++){
    tablica_serw[i].write(*ostatnia);//wskaznik ostatnia jest do tablicy ostatnia_pozycja, która służy do odczytu pozycji koncowych serw
    *ostatnia++;
    delay(200);
  }  
}
void setup() {
Serial.begin(9600);//ustawienie czestotliwosci probkowania dla portu szeregowego
// =============== definicja typow poszczegolnych pinow ============== 
pinMode(potencjometr,INPUT);
pinMode(dioda_ustawczy,OUTPUT);
pinMode(dioda_zapis,OUTPUT);
pinMode(dioda_odczyt,OUTPUT);
pinMode(przycisk_1, INPUT_PULLUP);
pinMode(przycisk_2,INPUT_PULLUP);
pinMode(przycisk_3,INPUT_PULLUP);
pinMode(przycisk_4,INPUT_PULLUP);
pinMode(przycisk_5,INPUT_PULLUP);
// ============ definicja przypisania serw to poszczegolnych pinow
serwo1.attach(serwo_1);
serwo2.attach(serwo_2);
serwo3.attach(serwo_3);
serwo4.attach(serwo_4);
serwo5.attach(serwo_5);
//=========definicja wskaznikow do struktur przechowujacych pamiec serw ====
tablica_pam[0].wskaznik = &tablica1[0];
tablica_pam[1].wskaznik = &tablica2[0];
tablica_pam[2].wskaznik = &tablica3[0];
tablica_pam[3].wskaznik = &tablica4[0];
tablica_pam[4].wskaznik = &tablica5[0];
}

void loop() {
  // ============= wybor ramienia, którym chcemy wykonać ruch ustawczy =========
  przycisk1();
  przycisk2();
  przycisk3();
  przycisk4();
  przycisk5();
//jesli nastapi nacisniecie przycisku 6 i faza pracy się zmieni to pętla zostanie opuszczona
  while(1){
    if(faza_pracy == 1){
      digitalWrite(dioda_ustawczy, LOW);
      ilosc_wcisniec = 0;
      ilosc_wcisniec2 = 0;//zerowanie tabeli wcisniec
      numer_serwa = 0;
      break;
    }
    digitalWrite(dioda_ustawczy, HIGH); // dioda sygnalizujaca
    przycisk1();
    przycisk2();
    przycisk3();
    przycisk4();
    przycisk5();
    przycisk6();
   
    if(numer_serwa != 0)
    ruchy_ustawcze(numer_serwa);   
  }

  //============= druga faza pracy ===============
  while (1){
    if(faza_pracy == 2){
      digitalWrite(dioda_zapis,LOW);
      tablica_pam[0].wskaznik = &tablica1[0];
      tablica_pam[1].wskaznik = &tablica2[0];
      tablica_pam[2].wskaznik = &tablica3[0];
      tablica_pam[3].wskaznik = &tablica4[0];
      tablica_pam[4].wskaznik = &tablica5[0];
      tablica_pam[0].indeks = 0;
      tablica_pam[1].indeks = 0;
      tablica_pam[2].indeks = 0;
      tablica_pam[3].indeks = 0;
      tablica_pam[4].indeks = 0;
      break;//po zakonczeniu zapisu nastepuje ustawienie wskaznikow na pierwsze komórki tabel do zapisu (w celu ich odczytu)
    }
    digitalWrite(dioda_zapis, HIGH);
    przycisk1();
    przycisk2();
    przycisk3();
    przycisk4();
    przycisk5();
    przycisk6();
    Serial.println("ilosc wcisniec");
    Serial.println(ilosc_wcisniec);
    Serial.println(numer_serwa);
    if(numer_serwa == 5){
      ruch_chwytaka(otwarcie_chwytaka);
    }
      else
      zapis_polozen(numer_serwa, ilosc_wcisniec);
    }
//====== powrot do pozycji poczatkowych ===============    
    powrot_do_poczatkowych();
    Serial.println("wrocilem");
//========== odczyt pozycji serw ================    
    while(1){
      if(faza_pracy == 3){
        digitalWrite(dioda_odczyt,LOW);//po wykonaniu zadania następuje powrót do początku programu i wyzerowanie flag 
        ilosc_wcisniec = 0;
        ilosc_wcisniec2 = 0;
        numer_serwa = 0;
        faza_pracy = 0;
        break;
      }
      digitalWrite(dioda_odczyt, HIGH);
      przycisk6();
      for(int i = 0; i <5; i++){
        odczyt_polozen(i,tablica_wcisniec[i]);
      }      
    }  
}
