# Hospital sys mombile app
pip install kivy
hospital_system/
├── main.py
├── models/
│   ├── __init__.py
│   ├── patient.py
│   ├── appointment.py
├── services/
│   ├── __init__.py
│   ├── patient_service.py
│   ├── appointment_service.py
└── utils/
    ├── __init__.py
    ├── validators.py
then ya mostafa shaheen tbtdy td5al    the Kivy Code:
main.py 
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.button import Button
from kivy.uix.label import Label
from kivy.uix.textinput import TextInput
from kivy.uix.screenmanager import ScreenManager, Screen

from services.patient_service import PatientService
from services.appointment_service import AppointmentService

patient_service = PatientService()
appointment_service = AppointmentService()


class HomeScreen(BoxLayout):
    def __init__(self, **kwargs):
        super(HomeScreen, self).__init__(**kwargs)
        self.orientation = 'vertical'
        self.add_widget(Label(text='Hospital Management System'))
        self.add_widget(Button(text='Manage Patients', on_press=self.switch_to_patient_screen))
        self.add_widget(Button(text='Schedule Appointment', on_press=self.switch_to_appointment_screen))

    def switch_to_patient_screen(self, instance):
        self.parent.current = 'patient_screen'

    def switch_to_appointment_screen(self, instance):
        self.parent.current = 'appointment_screen'


class PatientScreen(BoxLayout):
    def __init__(self, **kwargs):
        super(PatientScreen, self).__init__(**kwargs)
        self.orientation = 'vertical'
        self.add_widget(Label(text='Add Patient'))
        self.name_input = TextInput(hint_text='Name')
        self.age_input = TextInput(hint_text='Age')
        self.gender_input = TextInput(hint_text='Gender')
        self.add_widget(self.name_input)
        self.add_widget(self.age_input)
        self.add_widget(self.gender_input)
        self.add_widget(Button(text='Add', on_press=self.add_patient))
        self.add_widget(Button(text='Back', on_press=self.switch_to_home_screen))

    def add_patient(self, instance):
        name = self.name_input.text
        age = int(self.age_input.text)
        gender = self.gender_input.text
        patient_service.add_patient(len(patient_service.patients) + 1, name, age, gender)
        self.name_input.text = ''
        self.age_input.text = ''
        self.gender_input.text = ''
        print(f"Added patient: {name}, {age}, {gender}")

    def switch_to_home_screen(self, instance):
        self.parent.current = 'home_screen'


class AppointmentScreen(BoxLayout):
    def __init__(self, **kwargs):
        super(AppointmentScreen, self).__init__(**kwargs)
        self.orientation = 'vertical'
        self.add_widget(Label(text='Schedule Appointment'))
        self.patient_id_input = TextInput(hint_text='Patient ID')
        self.doctor_name_input = TextInput(hint_text='Doctor Name')
        self.date_time_input = TextInput(hint_text='Date and Time')
        self.add_widget(self.patient_id_input)
        self.add_widget(self.doctor_name_input)
        self.add_widget(self.date_time_input)
        self.add_widget(Button(text='Schedule', on_press=self.schedule_appointment))
        self.add_widget(Button(text='Back', on_press=self.switch_to_home_screen))

    def schedule_appointment(self, instance):
        patient_id = int(self.patient_id_input.text)
        doctor_name = self.doctor_name_input.text
        date_time = self.date_time_input.text
        appointment_service.schedule_appointment(len(appointment_service.appointments) + 1, patient_id, doctor_name, date_time)
        self.patient_id_input.text = ''
        self.doctor_name_input.text = ''
        self.date_time_input.text = ''
        print(f"Scheduled appointment for patient {patient_id} with doctor {doctor_name} at {date_time}")

    def switch_to_home_screen(self, instance):
        self.parent.current = 'home_screen'


class HospitalApp(App):
    def build(self):
        sm = ScreenManager()
        sm.add_widget(Screen(name='home_screen', content=HomeScreen()))
        sm.add_widget(Screen(name='patient_screen', content=PatientScreen()))
        sm.add_widget(Screen(name='appointment_screen', content=AppointmentScreen()))
        return sm


if __name__ == '__main__':
    HospitalApp().run()
  b3d kda b2a then y3ny  models/patient.py
class Patient:
    """A class representing a patient in the hospital."""

    def __init__(self, patient_id, name, age, gender):
        """Initialize a new patient."""
        self.patient_id = patient_id
        self.name = name
        self.age = age
        self.gender = gender

    def __repr__(self):
        """Return a string representation of the patient."""
        return f"Patient({self.patient_id}, {self.name}, {self.age}, {self.gender})"
        then ya shaheen model appointment.py 
        class Appointment:
    """A class representing an appointment in the hospital."""

    def __init__(self, appointment_id, patient_id, doctor_name, date_time):
        """Initialize a new appointment."""
        self.appointment_id = appointment_id
        self.patient_id = patient_id
        self.doctor_name = doctor_name
        self.date_time = date_time

    def __repr__(self):
        """Return a string representation of the appointment."""
        return (
            f"Appointment({self.appointment_id}, {self.patient_id}, "
            f"{self.doctor_name}, {self.date_time})"
        )
        then services/patient_service.py
        from models.patient import Patient

class PatientService:
    """A service class for managing patients."""

    def __init__(self):
        """Initialize the patient service with an empty patient list."""
        self.patients = []

    def add_patient(self, patient_id, name, age, gender):
        """Add a new patient to the system."""
        patient = Patient(patient_id, name, age, gender)
        self.patients.append(patient)
        return patient

    def get_patient(self, patient_id):
        """Retrieve a patient by their ID."""
        for patient in self.patients:
            if patient.patient_id == patient_id:
                return patient
        return None
        then services/appointment_service.py
        from models.appointment import Appointment

class AppointmentService:
    """A service class for managing appointments."""

    def __init__(self):
        """Initialize the appointment service with an empty appointment list."""
        self.appointments = []

    def schedule_appointment(self, appointment_id, patient_id, doctor_name, date_time):
        """Schedule a new appointment."""
        appointment = Appointment(appointment_id, patient_id, doctor_name, date_time)
        self.appointments.append(appointment)
        return appointment

    def get_appointment(self, appointment_id):
        """Retrieve an appointment by its ID."""
        for appointment in self.appointments:
            if appointment.appointment_id == appointment_id:
                return appointment
        return None
        then def validate_age(age):
    """Validate the age of a patient."""
    if age < 0:
        raise ValueError("Age cannot be negative.")
    return True

def validate_date_time(date_time):
    """Validate the format of the appointment date and time."""
    # Implement date-time validation logic here
    pass
    kda 5lst coding using pep8 format
    
    Running the Application

Navigate to the Project Directory:
cd path/to/hospital_system
Run the Application:
python main.py
Now the fun time w el mot3a yala ya shaheen htdy famed b2a w t3ml kda
Interact with the Application: You should see the Kivy application window with options to manage patients and schedule appointments.

Deploying to Mobile

To deploy the application to mobile devices, you can use Buildozer for Android and Xcode for iOS. Here are some resources to help you get started:

Kivy Documentation
Buildozer Documentation
Kivy iOS Documentation
By following these steps, you should be able to create a mobile-friendly hospital management system using Kivy.
Steps to deploy the application 
To deploy your Kivy application to mobile devices, you can use Buildozer for Android and Xcode for iOS. Below, I'll provide detailed steps for deploying the application to both platforms.

Deploying to Android using Buildozer

Install Buildozer and Dependencies:

You need to have Python 3.10 installed.
Install Buildozer and its dependencies. You can follow these steps:
bash
sudo apt update
sudo apt install -y python3-pip
pip install --upgrade cython
pip install buildozer
sudo apt install -y build-essential git zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev wget
sudo apt install -y libbz2-dev libsqlite3-dev
Initialize Buildozer:

Navigate to your project directory and initialize Buildozer.
bash
cd path/to/hospital_system
buildozer init
This will create a buildozer.spec file in your project directory.
Configure buildozer.spec File:

Open the buildozer.spec file and configure it according to your project's requirements.
Set the title, package.name, and package.domain fields.
Specify the requirements, including kivy.
Example configuration:

Code
[app]
title = HospitalSystem
package.name = hospitalsystem
package.domain = org.example
source.include_exts = py,png,jpg,kv,atlas
version = 0.1
requirements = python3,kivy
Build the APK:

Run the following command to build the APK:
buildozer -v android debug
This process will take some time and will generate the APK file in the bin directory.
Install the APK on Your Device:

Once the APK is built, connect your Android device to your computer and run:
adb install bin/hospitalsystem-0.1-debug.apk
Deploying to iOS using Xcode (on macOS)

Install Xcode:

Download and install Xcode from the Mac App Store.
Install Kivy iOS:

Install the Kivy iOS project tools:
pip install kivy-ios
Create the Kivy iOS Project:

Navigate to your project directory and create the Kivy iOS project.
bash
cd path/to/hospital_system
toolchain create hospitalsystem
cd hospitalsystem
Add Your Project Files:

Copy your project files into the newly created Kivy iOS project directory.
Build the Project:

Build the Kivy framework:
toolchain build kivy
Build your project:
toolchain build <your_project_name>
Open the Xcode Project:

Open the generated Xcode project:
toolchain open <your_project_name>-ios
Configure and Run the Project in Xcode:

In Xcode, set the appropriate signing and provisioning profiles.
Connect your iOS device and run the project.
Summary

By following these steps, you can deploy your Kivy application to both Android and iOS devices. For further details and troubleshooting, refer to the Kivy and Buildozer documentation:

Kivy Documentation
Buildozer Documentation
Kivy iOS Documentation
If you encounter any specific issues during the process, feel free to ask Amr Torky for help!
