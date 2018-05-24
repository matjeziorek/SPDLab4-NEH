# SPDLab4-NEH
#include <iostream>
#include <string>
#include <fstream>
#include <vector>
#include <algorithm>
#include <sstream>
#include <list>

using namespace std;

struct zad{
    vector<int> maszyny;
    int wZad, idZad;

    zad(){
        wZad=0; idZad=0;
    }

    void liczWage(int iloscMaszyn){
        wZad=0;
        for (int i=0; i<maszyny.size(); i++)
            wZad+=maszyny[i];
    }

    void wypZad(){
        cout << "Zad: " << idZad << " ";
        for (int i=0;i<maszyny.size();i++)
            cout << i+1 << ": " << maszyny[i] << " ";
        cout << "Waga: " << wZad <<endl;
    }
};

void wczytaj(list<zad> &msz, string nazwaPliku, int nrInst){
    fstream plik;
	plik.open(nazwaPliku.c_str(), ios::in);
    string tmpS;
    int tmpT, i=0, iMaszyn=0;

	if (plik.good()){
        for (int j=0;j<nrInst;j++){
            plik >> tmpS >> i >> iMaszyn;
            for (int k=0;k<i;k++){
                for (int l=0;l<iMaszyn;l++)
                    plik >> tmpT >> tmpT;
            }
        }
        plik >> tmpS >> i >> iMaszyn;
        for (int j=0;j<i;j++){
            zad tmpZ;
            for (int k=0;k<iMaszyn;k++){
                plik >> tmpT >> tmpT;
                tmpZ.maszyny.push_back(tmpT);
            }
            tmpZ.liczWage(iMaszyn);
            tmpZ.idZad=j+1;
            msz.push_back(tmpZ);
        }
	}
	else{
        cout << "Blad przy otwieraniu pliku" << endl;
        system("PAUSE");
        plik.close();
	}
	plik.close();
}

bool cmpZad(zad i, zad j){
return i.wZad>j.wZad;
}

int CMax(list<zad> &msz, int idZad, int idMaszyny){
    int cmax=0;

    if (idMaszyny<0)
        return 0;
    if (idZad<0)
        return 0;
    if ((idMaszyny==0)&&(idZad==0))
        return msz.front().maszyny[0];
    else{
        list<zad>::iterator i = msz.begin();
        advance(i,idZad);
        cmax=max(CMax(msz, idZad-1, idMaszyny), CMax(msz, idZad, idMaszyny-1))+(*i).maszyny[idMaszyny];
    }
        return cmax;
}

void neh(list<zad> &msz){
    int minCMax=0, idMinCMax=0;
    vector<int> CMaxT;
    list<zad> tmp;
    list<zad>::iterator i = msz.begin();
    tmp.push_back((*i));
    ++i;

    for (int j=1;j<msz.size();j++){
        tmp.push_back((*i));
        CMaxT.push_back(CMax(tmp, tmp.size()-1, tmp.front().maszyny.size()-1));

        list<zad>::iterator tmpi1 = tmp.begin();
        list<zad>::iterator tmpi2 = tmp.begin();
        zad tmpZ=tmp.back();
        tmp.pop_back();

        for(int k=0;k<tmp.size();k++){
            tmp.insert(tmpi1, tmpZ);
            tmpi2=tmp.begin();
            advance(tmpi2, k);
            CMaxT.push_back(CMax(tmp, tmp.size()-1, tmp.front().maszyny.size()-1));
            tmp.erase(tmpi2);
            tmpi1++;
        }
        tmp.push_back(tmpZ);

        minCMax=CMaxT[0];
        idMinCMax=0;
        for (int l=0;l<CMaxT.size();l++){
            if(CMaxT[l]<minCMax){
                minCMax=CMaxT[l];
                idMinCMax=l;
            }
        }
        if(idMinCMax>0){
            list<zad>::iterator tmpi3 = tmp.begin();
            advance(tmpi3,idMinCMax-1);
            tmp.insert(tmpi3, tmpZ);
            tmp.pop_back();
        }
        CMaxT.clear();
        i++;
    }
    msz.swap(tmp);
}

int main(){
    list<zad> inst;
    for (int nrInst=0;nrInst<120;nrInst++){

        wczytaj(inst, "FSTA.txt", nrInst);
        inst.sort(cmpZad);
        cout << "nrInstancji: " << nrInst <<endl;
        neh(inst);
        for (list<zad>::iterator i = inst.begin(); i != inst.end(); ++i){
			cout << (*i).idZad << " ";
		}
		cout << endl << "CMax: " << CMax(inst, inst.size()-1, inst.back().maszyny.size()-1) << endl;
        inst.clear();
        system("PAUSE");
    }
return 0;
}
