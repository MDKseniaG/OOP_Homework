class Student:
    """
    Конструктор класса Student.
    :param name: Имя студента
    :param surname: Фамилия студента
    :param gender: Пол студента
    :param finished_courses: Список законченных курсов
    :param courses_in_progress: Список текущих курсов
    :param grades: Словарь (предмет: оценки) от преподавателей
    :param ratings: Словарь оценки лекции (предмет: оценки) от ученика преподавателям
    :param average_grade: Средняя оценка
    :param student_list: Список всех экземпляров класса студент
    """

    student_list = []

    def __init__(self, name, surname, gender='Не указан'):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}
        self.ratings = {}
        self.average_grade = 0
        self.student_list.append(self)

    def add_courses_in_progress(self, *courses):
        if not courses:
            return False
        self.courses_in_progress.extend(courses)

    def add_finished_courses(self, *courses):
        if not courses:
            return False
        self.finished_courses.extend(courses)

    def rate_lectors(self, course, lecturer, rating):
        if (
            course in self.courses_in_progress and
            isinstance(lecturer, Lectures) and
            course in lecturer.courses_attached and
            0 <= rating <= 10
        ):
            lecturer.ratings.setdefault(course, []).append(rating) 
        else:
            return 'Ошибка: Невозможно выставить оценку'

    def average_grades(self):
        if not self.grades:
            return 0
        total_sum = sum(sum(grades) for grades in self.grades.values())
        total_count = sum(len(grades) for grades in self.grades.values())
        self.average_grade = total_sum / total_count if total_count != 0 else 0
        return self.average_grade

    def __lt__(self, other):
        if not isinstance(other, Student):
            print('Сравниваем не студентов')
            return
        return self.average_grade < other.average_grade

    def __gt__(self, other):
        if not isinstance(other, Student):
            print('Сравниваем не студентов')
            return
        return self.average_grade > other.average_grade

    def __str__(self):
        courses_in_progress = ", ".join(self.courses_in_progress) if self.courses_in_progress else "Нет курсов в процессе"
        finished_courses = ", ".join(self.finished_courses) if self.finished_courses else "Нет завершенных курсов"

        return (f'Студент:\n'
                f'Имя: {self.name}\n'
                f'Фамилия: {self.surname}\n'
                f'Средняя оценка за домашние задания: {self.average_grades():.2f}\n'
                f'Курсы в процессе изучения: {courses_in_progress}\n'
                f'Завершенные курсы: {finished_courses}\n')
    
class Mentor:
    mentor_list = []
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []
        self.mentor_list.append(self)
        
    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course] += [grade]
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'
        
    def add_courses_in_attached(self, *courses):
        if not courses:
            return False
        self.courses_attached.extend(courses)

    def __str__(self):
        return (
            f'Ментор:\n'
            f'Имя: {self.name}\n'
            f'Фамилия: {self.surname}\n'
            f'Закреплен к курсам: {", ".join(self.courses_attached)}\n'
            )
        
class Reviewer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)

    def __str__(self):
        return (
            f'Ревьюер:\n'
            f'Имя: {self.name}\n'
            f'Фамилия: {self.surname}\n'
            f'Закреплен к курсам: {", ".join(self.courses_attached)}\n'
            )

class Lectures(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.ratings = {}  
        self.courses_attached = []  
        self.average_grade = 0

    def average_grades(self):
        if not self.ratings:  
            return 0
        total_sum = sum(sum(grades) for grades in self.ratings.values()) 
        total_count = sum(len(grades) for grades in self.ratings.values()) 
        self.average_grade = total_sum / total_count if total_count != 0 else 0
        return self.average_grade

    def __lt__(self, other):
        if not isinstance(other, Student):
            print('Сравниваем не студентов')
            return
        return self.average_grade < other.average_grade

    def __gt__(self, other):
        if not isinstance(other, Student):
            print('Сравниваем не студентов')
            return
        return self.average_grade > other.average_grade

    def __str__(self):
        courses_attached = ", ".join(self.courses_attached) if self.courses_attached else "Нет закрепленных курсов"
        return (f'Лектор:\n'
                f'Имя: {self.name}\n'
                f'Фамилия: {self.surname}\n'
                f'Средняя оценка за лекции: {self.average_grades():.2f}\n'
                f'Закреплен к курсам: {courses_attached}\n')



# 

lecturer_1 = Lectures('Иван', 'Петров')
lecturer_2 = Lectures('Петр', 'Иванов')
reviewer_1 = Reviewer('Анна', 'Десницкая')
reviewer_2 = Reviewer('Ярослава', 'Зайцева')
student_1 = Student('Елена', 'Шварцман')
student_2 = Student('Андрей', 'Агафонов')

# 

student_1.add_courses_in_progress("Data sceince", "Machine learning", "Data analytics", "Fullstuck developer")
student_1.add_finished_courses("System administrator", "SMM specialist", "Product management", "Python developer", "English for IT", "PR management", "Java developer")
student_2.add_courses_in_progress("Data science", "Machine learning", "Data analytics", "Fullstuck developer", "Product management")
student_2.add_finished_courses("Project communications", "Teamwork", "Business consultant", "Sales specialist", "Frontend developer", "Negotiation skills", "Leadership skills", "Team management")

lecturer_1.add_courses_in_attached("Data science", "Machine learning", "Data analytics", "Fullstuck developer", "Product management", "English for IT", "SEO specialist", "Java specialist")
lecturer_2.add_courses_in_attached("Data science", "Machine learning", "Data analytics", "Fullstuck developer", "Python developer", "Product management", "Team management", "Business consultant")
reviewer_1.add_courses_in_attached("Data science", "Machine learning", "Data analytics", "Fullstuck developer", "Frontend developer", "Product management", "Sales specialist", "Teamwork")
reviewer_2.add_courses_in_attached("Data science", "Machine learning", "Data analytics", "Python developer", "Product management", "SMM specialist", "Sales specialist", "Fullstuck developer")

# 

reviewer_1.rate_hw(student_1, 'Data science', 7)
reviewer_1.rate_hw(student_1, 'Machine learning', 9)
reviewer_1.rate_hw(student_1, 'Data analytics', 7)
reviewer_1.rate_hw(student_1, 'Fullstuck developer', 8)

reviewer_2.rate_hw(student_1, 'Data science', 9)
reviewer_2.rate_hw(student_1, 'Machine learning', 7)
reviewer_2.rate_hw(student_1, 'Data analytics', 8)
reviewer_2.rate_hw(student_1, 'Fullstuck developer', 8)

lecturer_1.rate_hw(student_1, 'Data science', 6)
lecturer_1.rate_hw(student_1, 'Machine learning', 7)
lecturer_1.rate_hw(student_1, 'Data analytics', 9)
lecturer_1.rate_hw(student_1, 'Fullstuck developer', 10)

lecturer_2.rate_hw(student_1, 'Data science', 7)
lecturer_2.rate_hw(student_1, 'Machine learning', 10)
lecturer_2.rate_hw(student_1, 'Data analytics', 9)
lecturer_2.rate_hw(student_1, 'Fullstuck developer', 6)

student_1.rate_lectors('Data science', lecturer_1, 7)
student_1.rate_lectors('Machine learning', lecturer_1, 8)
student_1.rate_lectors('Data analytics', lecturer_1, 9)
student_1.rate_lectors('Fullstuck developer', lecturer_1, 8)

student_1.rate_lectors('Data science', lecturer_2, 6)
student_1.rate_lectors('Machine learning', lecturer_2, 9)
student_1.rate_lectors('Data analytics', lecturer_2, 7)
student_1.rate_lectors('Fullstuck developer', lecturer_2, 8)

student_2.rate_lectors('Data science', lecturer_1, 8)
student_2.rate_lectors('Machine learning', lecturer_1, 9)
student_2.rate_lectors('Data analytics', lecturer_1, 10)
student_2.rate_lectors('Fullstuck developer', lecturer_1, 7)
student_2.rate_lectors('Product management', lecturer_1, 7)

student_2.rate_lectors('Data science', lecturer_2, 6)
student_2.rate_lectors('Machine learning', lecturer_2, 9)
student_2.rate_lectors('Data analytics', lecturer_2, 7)
student_2.rate_lectors('Fullstuck developer', lecturer_2, 8)
student_2.rate_lectors('Product management', lecturer_2, 8)

# 

reviewer_1.rate_hw(student_2, 'Data science', 8)
reviewer_1.rate_hw(student_2, 'Machine learning', 8)
reviewer_1.rate_hw(student_2, 'Data analytics', 8)
reviewer_1.rate_hw(student_2, 'Fullstuck developer', 8)
reviewer_1.rate_hw(student_2, 'Product management', 8)

reviewer_2.rate_hw(student_2, 'Data science', 10)
reviewer_2.rate_hw(student_2, 'Machine learning', 9)
reviewer_2.rate_hw(student_2, 'Data alanytics', 10)
reviewer_2.rate_hw(student_2, 'Fullstuck developer', 9)
reviewer_2.rate_hw(student_2, 'Product management', 9)

lecturer_1.rate_hw(student_2, 'Data science', 6)
lecturer_1.rate_hw(student_2, 'Machine learning', 7)
lecturer_1.rate_hw(student_2, 'Data analytics', 8)
lecturer_1.rate_hw(student_2, 'Fullstuck developer', 9)
lecturer_1.rate_hw(student_2, 'Product management', 9)

lecturer_2.rate_hw(student_2, 'Data science', 10)
lecturer_2.rate_hw(student_2, 'Machine learning', 9)
lecturer_2.rate_hw(student_2, 'Data analytics', 8)
lecturer_2.rate_hw(student_2, 'Fullstuck developer', 7)
lecturer_2.rate_hw(student_2, 'Product management', 7)

# 

print(lecturer_1)
print(lecturer_2)
print(reviewer_1)
print(reviewer_2)
print(student_1)
print(student_2)

# 

def calc_average_students(students, course):
    student_courses = []
    for student in students:
        for k, v in student.grades.items():
            if k == course:
                student_courses.extend(v)
    summa = sum(student_courses)
    average_grade = summa / len(student_courses)
    return average_grade

def calc_average_lectures(lectures, course):
    lecture_courses = []
    for lecture in lectures:
        if isinstance(lecture, Lectures) and course in lecture.ratings:
            lecture_courses.extend(lecture.ratings[course])
    
    if not lecture_courses:  
        return 0  
    
    summa = sum(lecture_courses)
    average_grade = summa / len(lecture_courses)
    return average_grade

print(f'Средняя оценка за ДЗ по всем студентам курса "Data science": {calc_average_students(Student.student_list, 'Data science')}')
print(f'Средняя оценка за ДЗ по всем студентам курса "Data analytics": {calc_average_students(Student.student_list, 'Data analytics')}')
print(f'Средняя оценка за ДЗ по всем студентам курса "Prodcut management": {calc_average_students(Student.student_list, 'Product management')}')

print(f'Средняя оценка за лекции по всем лекторам курса "Data science": {calc_average_lectures(Mentor.mentor_list, "Data science")}')
print(f'Средняя оценка за лекции по всем лекторам курса "Data analytics": {calc_average_lectures(Mentor.mentor_list, "Data analytics")}')
print(f'Средняя оценка за лекции по всем лекторам курса "Product management": {calc_average_lectures(Mentor.mentor_list, "Product management")}')

# reviewer = Reviewer('Uniq', 'Reviewer')
# reviewer.courses_attached += ['Python']
# reviewer.rate_hw(best_student, 'Python', 2)
# reviewer.rate_hw(best_student, 'Python', 3)
# reviewer.rate_hw(best_student, 'Python', 6)

# cool_mentor = Mentor('Some', 'Buddy')
# cool_mentor.courses_attached += ['Python']

# cool_mentor.rate_hw(best_student, 'Python', 10)
# cool_mentor.rate_hw(best_student, 'Python', 9)
# cool_mentor.rate_hw(best_student, 'Python', 8)

# print(best_student)
