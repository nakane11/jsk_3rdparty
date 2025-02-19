cmake_minimum_required(VERSION 2.8.3)
project(google_chat_ros)

find_package(
  catkin REQUIRED COMPONENTS
  catkin_virtualenv REQUIRED
  rospy
  actionlib_msgs
  std_msgs
  message_generation
  )

catkin_python_setup()

add_message_files(
  DIRECTORY msg
  )

add_action_files(
  FILES
  SendMessage.action
  )

generate_messages(
  DEPENDENCIES
  std_msgs
  actionlib_msgs
  )

catkin_package()

# generate the virtualenv
catkin_generate_virtualenv(
  INPUT_REQUIREMENTS requirements.in
  PYTHON_INTERPRETER python3
  USE_SYSTEM_PACKAGES FALSE
  CHECK_VENV FALSE
  )

# install
# euslisp
file(GLOB EUSLISP_SCRIPTS scripts/*.l)
install(FILES ${EUSLISP_SCRIPTS}
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  )

# python
file(GLOB PYTHON_SCRIPTS scripts/*.py)
catkin_install_python(
  PROGRAMS ${PYTHON_SCRIPTS}
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  )
# python requirements
install(FILES requirements.txt
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
  )

# launch
install(DIRECTORY launch
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
  )

# import test
if(CATKIN_ENABLE_TESTING)
  find_package(catkin REQUIRED COMPONENTS catkin_virtualenv rostest)
  catkin_install_python(
    PROGRAMS test/test_import.py
    DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
    )
  add_rostest(test/import.test
    DEPENDENCIES ${PROJECT_NAME}_generate_virtualenv
    )
endif()
