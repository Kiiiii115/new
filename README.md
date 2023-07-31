from tkinter import *

root = Tk()
root.title("더스트커버 내구지수 예측")
root.geometry("400x400") #가로 x 세로
root.resizable(False, False) # 창 크기 변경 불가 

chkvar1 = IntVar() # chkvar 에 int 형으로 값을 저장한다
chkvar2 = IntVar()
chkvar3 = IntVar()
chkbox1 = Checkbutton(root, text="해석 모델 파일과 프로그램 동일 폴더 확인", variable=chkvar1)
chkbox1.pack()
chkbox2 = Checkbutton(root, text="해석 모델 내 휠센터, 볼트좌면 단차 형성 확인", variable=chkvar2)
chkbox2.pack()
chkbox3 = Checkbutton(root, text="해석 모델 재질 입력 확인", variable=chkvar3)
chkbox3.pack()

#결과 파일을 열어서 1~6차 고유 진동수 추출
f = open("D:/DCVR/1234.txt", 'r')
text = f.read()
frequency = []
a = 0
text = text[text.find("Subcase Mode"):] #해석 결과 값 위에 있는 subcase mode 기준으로 자르기
while a<6 : # 6회 반복
    x = text.find("E+0")
    if text.find("E+0") > 1 and text[x+3] == "2": #E+02: 100배
        frequency.append(int((float(text[x-8 : x-4])*100)))
    elif text.find("E+0") > 1 and text[x+3] == "1": #E+01: 10배
        frequency.append(int((float(text[x-8 : x-4])*10)))
    text = text[x+50:] #읽어낸 고유진동수값의 줄 자르기
    a +=1
f.close()

#def btncmd():
 #   카티아 실행 및 고유진동수 해석 매크로 실행하는 명령어 기입 필요

def btncmd1():
    if chkvar1.get() == 1 and chkvar2.get() == 1 and chkvar3.get() == 1 :  # 체크박스가 1일때(체크 有)만 실행
        if frequency[0] < 70 : #1차 고유진동수가 낮을때 강성 부족(+빨간색) 경고 뜨게 해봤는데 어떨까요??
            label1 = Label(root, text="1차 : 강성 부족", fg = "red")
            e1 = Entry(root, width=15)
            label1.pack()
            e1.pack()
        else :
            label1 = Label(root, text="1차")
            e1 = Entry(root, width=15)
            label1.pack()
            e1.pack()
                
        label2 = Label(root, text="2차")
        e2 = Entry(root, width=15)
        label2.pack()
        e2.pack()
        label3 = Label(root, text="3차")
        e3 = Entry(root, width=15)
        label3.pack()
        e3.pack()
        label4 = Label(root, text="4차")
        e4 = Entry(root, width=15)
        label4.pack()
        e4.pack()
        label5 = Label(root, text="5차")
        e5 = Entry(root, width=15)
        label5.pack()
        e5.pack()
        label6 = Label(root, text="6차")
        e6 = Entry(root, width=15)
        label6.pack()
        e6.pack()
        
        '''
        frf = open("D:\DCVR\code\index2.html", "r") # 고유진동수 Report 파일 경로 및 파일명 입력
        frfvalues = frf.read()
        frf1 = frfvalues[1] # 00 번째 줄 00번째 문자 또는 특정 문자 앞의 숫자 읽는 명령 입력 필요
        frf2 = frfvalues[2]
        frf3 = frfvalues[3] 
        frf4 = frfvalues[4]
        frf5 = frfvalues[5]
        frf6 = frfvalues[6]
    

        e1.insert(0, frf1) # e1 Entry(빈칸) 안에 고유진동수 값 입력하는 명령
        e2.insert(0, frf2)
        e3.insert(0, frf3)
        e4.insert(0, frf4)
        e5.insert(0, frf5)
        e6.insert(0, frf6)
    '''
        
        e1.insert(0, frequency[0]) # 위에서 생성한 FREQUENCY 자동 삽입
        e2.insert(0, frequency[1])
        e3.insert(0, frequency[2])
        e4.insert(0, frequency[3])
        e5.insert(0, frequency[4])
        e6.insert(0, frequency[5])

        btn2.pack() # 버튼 1 실행 후 버튼 2 생성

btn1 = Button(root, text="고유진동수 해석", command=btncmd1)
btn1.pack()

def btncmd2():
    e7 = Entry(root, width=15)
    e7.pack()

    import numpy as np
    import pandas as pd
    from sklearn.ensemble import RandomForestRegressor

    # Database 폴더 경로 입력 필요
    dataset =pd.read_csv('D:\DCVR\code\A_type_dcvr_train_database_221213_refined.csv')

    X = dataset.iloc[:, :-1].values
    Y = dataset.iloc[:, -1].values

    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test= train_test_split(X, Y, test_size= 0.1, random_state= 1)

    regressor=RandomForestRegressor()
    regressor.fit(X_train,Y_train)
      
    e7.insert(0, regressor.predict([[frequency[0], frequency[1], frequency[2], frequency[3], frequency[4], frequency[5]]]))  # 대괄호 내 frf1~frf6 자동 입력 명령 필요

btn2 = Button(root, text="내구지수 예측", command=btncmd2)

root.mainloop()
