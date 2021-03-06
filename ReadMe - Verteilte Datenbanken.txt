﻿
Überlegung für verteilte Datenbank
==================================

Lagerbestand:
- Insert, Update (+/- Anzahl Differenz, Lager, MHD), Delete

Artikelstamm
- IUD

Einkaufszettel
- Anzahl, gekauft



Annahmen:

- In der Gruppe (z.B. Familie) gibt es nur EINE Master Datenbank.
- Alle replizieren nur gegen die Master Datenbank.
- Jede Datenbank kann als Master Datenbank definiert werden, dann aber:
  * kann sie aber nicht mehr replizieren
  * Änderungs-Protokoll wird dann gelöscht
- Wenn Master Datenbank ins Slave konvertiert wird, dann:
  * werden alle lokalen Daten davor gelöscht,
  * die Daten vom Master werden auf das Gerät übertragen

Client
- Markt sich z.B. Mengen- und Datenänderungen (z.B. 2 entnommen, 1 gelöscht, Artikel A in Einkaufskorb)
- Replikation nur mit dem Master möglich.
- Zuerst werden Änderungen übertragen und dann den Ist Zustand geholt.


Beispiel:

    Master                              Teilnehmer 1                        Teilenhmer 2
    
    Artikel Menge
    ------- -----
    A       5
                                        => START der Replikation
                                        - Keine Änderungen
                                        - Aktuellen Stand abrufen
                                        => ENDE der Replikation
                                        
                                        Artikel Menge  Änderung
                                        ------- -----  --------
                                        A       5      
                                                                            => START der Replikation
                                                                            - Keine Änderungen
                                                                            - Aktuellen Stand abrufen
                                                                            => ENDE der Replikation
                                                                            
                                                                            Artikel Menge  Änderung
                                                                            ------- -----  --------
                                                                            A       5      
                                                                            
                                                                            
                                        Änderung Artikel A Menge -1
                                        Artikel Menge  Änderung
                                        ------- -----  --------
                                        A       4      -1
                                        
                                                                            Änderung Artikel A Menge -2
                                                                            Artikel Menge  Änderung
                                                                            ------- -----  --------
                                                                            A       3      -2
    Artikel Menge                                    
    ------- -----                       => START der Replikation
    A       5-1       <---------------- Beim Artikel A Menge -1
    A       4         ----------------> Aktuelle Menge 4
                                        => ENDE der Replikation
    
                                        Artikel Menge  Änderung
                                        ------- -----  --------
                                        A       4      
    Artikel Menge
    ------- -----                                                           => START der Replikation
    A       4-2       <---------------------------------------------------- Beim Artikel A Menge -2
    A       2         ----------------------------------------------------> Aktuelle Menge 2
                                                                            => ENDE der Replikation

                                                                            Artikel Menge  Änderung
                                                                            ------- -----  --------
                                                                            A       2      


    Artikel Menge
    ------- -----                       => START der Replikation
    A       2                           keine Änderungen
    A       2         ----------------> Aktuelle Menge 2
                                        => ENDE der Replikation
                                        
                                        Artikel Menge  Änderung
                                        ------- -----  --------
                                        A       2      
                                        
                                        
                                        
                                        
Technologie
===========

Eigene IP Adresse ermitteln:

            WifiManager wifiManager = (WifiManager)this.ApplicationContext.GetSystemService(Service.WifiService);
            int ip = wifiManager.ConnectionInfo.IpAddress;

            string addressAsString = string.Format("{0}.{1}.{2}.{3}",
                            (ip & 0xff),
                            (ip >> 8 & 0xff),
                            (ip >> 16 & 0xff),
                            (ip >> 24 & 0xff));
                  